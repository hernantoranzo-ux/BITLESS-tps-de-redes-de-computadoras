<div align="center">

<img src="./images/image1.png" width="200" alt="Logo UNC" />

# Universidad Nacional de Córdoba

**Facultad de Ciencias Exactas, Físicas y Naturales**
**Cátedra Redes de Computadora**

## Trabajo Práctico 5

</div>

**Alumnos:**

| Nombres                       | DNI        |
|:-----------------------------:|:----------:|
| Monetti, Francisco            | 46590584   |
| Toranzo, Hernan               | 44345504   |
| Silva, Jonathan Ariel         | 42641133   |
| Barron Saez, Lautaro          | 44909179   |
| Hernandez, Gonzalo Nicolas    | 42246081   |
| Michaud, Facundo              | 47302808   |

Octubre 2026

---

## Índice

1. [ICMP y primer contacto con Wireshark](#1-icmp-y-primer-contacto-con-wireshark)
   - [a) Qué es ICMP](#a-qué-es-icmp)
   - [b) Relación entre ICMP e IP](#b-relación-entre-icmp-e-ip)
   - [c) Ping, Echo Request y Echo Reply](#c-ping-echo-request-y-echo-reply)
   - [d) Información mínima de un mensaje ICMP de tipo Echo](#d-información-mínima-de-un-mensaje-icmp-de-tipo-echo)

2. [Protocolo ARP](#2-protocolo-arp)
   - [a) Qué es ARP y su ubicación en el modelo en capas](#a-qué-es-arp-y-su-ubicación-en-el-modelo-en-capas)
   - [b) ARP Request y ARP Reply](#b-arp-request-y-arp-reply)
   - [c) Caché ARP](#c-caché-arp)
   - [d) Procedimiento de resolución de direcciones](#d-procedimiento-de-resolución-de-direcciones)
   - [e) Inspección de la caché ARP y verificación del gateway](#e-inspección-de-la-caché-arp-y-verificación-del-gateway)
   - [f) Generación y captura de tráfico ARP](#f-generación-y-captura-de-tráfico-arp)


3. [TCP y UDP "a mano" con ncat](#3-tcp-y-udp-a-mano-con-ncat)
   - [Conceptos previos](#conceptos-previos)
      - [a) Establecimiento de conexión](#a-establecimiento-de-conexión)
      - [b) Puertos e identificación](#b-puertos-e-identificación)
      - [c) Procesos en escucha](#c-procesos-en-escucha)
   - [Análisis de capturas](#análisis-de-capturas)
      - [a) Sincronización inicial](#a-sincronización-inicial)
      - [b) Confirmación de entrega](#b-confirmación-de-entrega)
      - [c) Tamaño de cabeceras](#c-tamaño-de-cabeceras)
      - [d) Cierre de conexión](#d-cierre-de-conexión)
      - [e) Confiabilidad y paquetes extra](#e-confiabilidad-y-paquetes-extra)
      - [f) Rechazo de conexión](#f-rechazo-de-conexión)  
4. [Servidor TCP - TP5 Redes de Computadoras](#4-servidor-tcp---tp5-redes-de-computadoras)

---

## CONSIGNAS

## 1) ICMP y primer contacto con Wireshark

### a) Qué es ICMP

**ICMP (Protocolo de mensajes de control de internet)** es un protocolo que forma parte de la capa de red en la arquitectura **TCP/IP**. Se encarga de brindar información de retroalimentación sobre distintos problemas que puedan surgir en el entorno de la comunicación, lo que permite que los routers o computadores notifiquen al equipo de origen sobre estos mismos. 

Los mensajes **ICMP** están compuestos por una cabecera de 64 bits que posee los siguientes campos:
- `Tipo` (8 bits): Especifica el tipo de mensaje ICMP.
- `Código` (8 bits): Especifica parámetros del mensaje que se pueden codificar en
uno o unos pocos bits.
- `Suma de comprobación` (16 bits): Es una suma de comprobación del mensaje ICMP entero. Utiliza el mismo algoritmo de suma de comprobación que en IP.
- `Parámetros` (32 bits): Se usa para especificar parámetros más largos.

**ICMP** no transmite información directa entre usuarios como **TCP** o **UDP**, simplemente se usa para la gestión y diagnóstico de una red. Algunos de los mensajes usados para ello son:
- **Destino inalcanzable:** Un dispositivo de encaminamiento puede emitir este mensaje si no sabe cómo alcanzar a la red de destino.
- **Tiempo excedido:** Un dispositivo de encaminamiento puede emitir este mensaje si el tiempo de vida del correspondiente datagrama ya expiró.
- **Problema de parámetro:** Un dispositivo de encaminamiento puede emitir este mensaje si hay un error sintáctico o semántico en la cabecera IP.
- **Ralentización del origen:** Proporciona una forma de control de flujo. Puede ser emitido por un dispositivo de encaminamiento o un computador de destino, solicitando al equipo de origen que reduzca la tasa de datos a la que envía el tráfico al destino. Puede suceder cuando el dispositivo de encaminamiento o computador destino deben descartar datagramas por tener sus buffers llenos.
- **Redirección:** Un dispositivo de encaminamiento puede emitir este mensaje para informar sobre una mejor ruta para un destino en específico.
- **Eco y respuesta a eco:** Son mensajes que proporcionan un mecanismo para comprobar que la comunicación entre dos dispositivos es posible.
- **Marca de tiempo y respuesta a marca de tiempo:** Son mensajes que proporcionan un mecanismo para muestrear las características respecto del retardo en un conjunto de redes.
- **Petición de máscara de dirección y respuesta a máscara de dirección:** Son mensajes útiles en entornos con subredes. Permite a un computador conocer la máscara de dirección que utiliza en la LAN a la que está conectado.

### b) Relación entre ICMP e IP

**ICMP es una parte integral del protocolo IP:** Funciona como protocolo auxiliar en la capa de red para brindar mecanismos de gestión y diagnóstico que el protocolo IP no posee por sí solo. Aunque ICMP esté en el mismo nivel que IP, termina siendo un usuario de IP. Cuando se construye un mensaje ICMP, este se pasaría a IP y sería encapsulado por una cabecera IP, por lo que puede decirse que viaja dentro de IP. 

Un receptor puede saber que el contenido de un paquete IP es ICMP mediante un campo en la cabecera IP que fue reservado específicamente para identificar qué protocolo de la capa superior o auxiliar está contenido en el paquete: campo Protocolo (en IPv4) o campo Cabecera siguiente (en IPv6). De esta forma el receptor sabe que, cuando recibe el paquete, debe derivarlo al respectivo módulo ICMP para su interpretación.

### c) Ping, Echo Request y Echo Reply 

`Ping` es una herramienta para el diagnóstico de una red que utiliza el protocolo **ICMP** para verificar si un determinado dispositivo está activo y es alcanzable en la red, para así poder evaluar la conexión entre dos dispositivos. De acuerdo a esta definición, Ping correspondería al tipo de mensaje Eco y respuesta a eco (**Echo Request** y **Echo Reply**) que mencionamos anteriormente.

- **Echo Request:** Corresponde al mensaje **ICMP** que envía el dispositivo de origen (con `Ping`) a un destino para confirmar la recepción y conectividad.
- **Echo Reply:** Corresponde al mensaje **ICMP** que el dispositivo destino debe devolver al origen para confirmar que la transmisión fue exitosa.

Los campos de **ICMP** que permiten distinguirlos son `Tipo`, `Código` y los campos `Identificador` (16 bits) y `Número de secuencia` (16 bits) dentro del campo `Parámetros`.


### d) Información mínima de un mensaje ICMP de tipo Echo

La información mínima que contiene un mensaje **ICMP** de tipo **Echo** es:
- `Tipo`: Para un **Echo Request** toma el valor 8, para un **Echo Reply** toma el valor 0.
- `Código`: Es siempre 0 para los mensajes **Echo**.
- `Suma de comprobación`: Se calcula sobre el **ICMP** completo, no podemos definirlo sin más.
- `Identificador`: Permite diferenciar el motivo que generó el mensaje **Echo**.
- `Número de secuencia`: Es un contador incremental que permite emparejar cada respuesta recibida con la respectiva solicitud que lo generó.

---

## 2) Protocolo ARP

### a) Qué es ARP y su ubicación en el modelo en capas

**ARP (Address Resolution Protocol)** resuelve el problema de la traducción de direcciones: permite a un equipo descubrir de forma dinámica la dirección MAC física asociada a una dirección IP conocida dentro de la misma red local. Sin ARP, los dispositivos sabrían a qué IP enviar un paquete, pero no podrían construir la trama Ethernet física para entregárselo en la LAN.

La ubicación de ARP en el modelo en capas (OSI / TCP-IP) es un tema de debate clásico en redes, ya que presenta características de ambas capas:

**Argumentos para ubicarlo en la Capa 2 (Enlace de datos):**
- **Encapsulación directa:** Los mensajes ARP se empaquetan directamente dentro de una trama Ethernet (utilizando el campo `EtherType = 0x0806`), sin agregar una cabecera IP ni viajar dentro de un paquete IP.
- **Alcance local:** Opera exclusivamente dentro del dominio de difusión de la red local (LAN) usando direcciones de difusión MAC (`ff:ff:ff:ff:ff:ff`) y no puede ser encaminado a través de un router.

**Argumentos para ubicarlo en la Capa 3 (Red) o como "Capa 2.5":**
- **Función de servicio a IP:** Su único propósito existe en función de IP (Capa 3), ya que maneja información de direcciones de red (IPv4) para resolverlas.
- **Protocolo auxiliar/control:** Al igual que otros protocolos de control que asisten a la capa de red, actúa como una interfaz o puente intermedio entre la Capa 2 y la Capa 3. Por esta razón, gran parte de la literatura técnica lo clasifica en la **Capa 3** o lo define informalmente como un protocolo de **Capa 2.5**.

### b) ARP Request y ARP Reply

Un **ARP Request (Solicitud ARP)** es el mensaje que emite un host cuando conoce la dirección IP de destino en su red local pero requiere descubrir su dirección MAC física para empaquetar la trama Ethernet. Esta solicitud se envía mediante difusión (*broadcast*) a todas las estaciones de la red local utilizando la dirección MAC `ff:ff:ff:ff:ff:ff`, dejando el campo de la dirección física buscada en ceros.

Por otro lado, un **ARP Reply (Respuesta ARP)** es la contestación emitida únicamente por el equipo cuya dirección IP coincide con la consultada, informando su dirección MAC física. A diferencia de la solicitud, la respuesta se envía de forma directa y monodifusión (*unicast*) a la máquina consultante, ya que el destinatario aprendió la MAC de origen a partir de la solicitud previa.

### c) Caché ARP

La **caché ARP** es una tabla o memoria temporal mantenida por el sistema operativo de cada equipo que guarda las correspondencias aprendidas entre direcciones IP y direcciones MAC físicas de la red local. Existe para optimizar el rendimiento de la red y evitar la saturación por tráfico de difusión; de este modo, cuando una computadora necesita enviar datos a una IP, primero consulta su propia tabla para obtener la MAC correspondiente en lugar de emitir un *ARP Request* por difusión cada vez que envía un paquete. Dado que las configuraciones de red pueden cambiar, estas entradas tienen un tiempo de expiración dinámico para refrescarse periódicamente y mantener la información actualizada.

### d) Procedimiento de resolución de direcciones

El procedimiento que sigue un equipo para resolver esta situación consta de los siguientes pasos:

- **Consulta en la caché local:** En primer lugar, el sistema operativo consulta su **tabla o caché ARP local** para verificar si ya existe una entrada guardada con la dirección MAC asociada a esa IP. Si la dirección física se encuentra en la caché, se toma de forma inmediata y se construye la trama Ethernet para transmitir los datos.
- **Solicitud y respuesta ARP:** Si la dirección MAC no está registrada en la caché, el equipo emite una solicitud **ARP Request** mediante difusión (*broadcast*) dirigida a toda la red local (`ff:ff:ff:ff:ff:ff`), preguntando cuál de las máquinas posee la IP de destino. La computadora que reconoce la dirección IP como propia responde con un **ARP Reply** en modo monodifusión (*unicast*), entregando su dirección MAC física. Al recibir esta respuesta, la máquina emisora guarda la relación en su caché ARP para futuras comunicaciones y procede a encapsular y enviar la trama Ethernet al destino.

### e) Inspección de la caché ARP y verificación del gateway

Para observar el contenido y estado de la tabla ARP en el sistema operativo Linux se ejecutó el comando `ip neigh show` en la terminal (equivalente a `arp -a` en Windows):

![Caché ARP](./images/punto%202/cache_arp.png)

*(Aclaración metodológica: Debido a que la realización del trabajo práctico se dividió de forma colaborativa entre los integrantes del grupo, el Punto 1 y el Punto 2 fueron ejecutados en equipos y redes domésticas diferentes. Por tal motivo, los valores numéricos concretos de las direcciones MAC físicas y de los gateways difieren entre las capturas de cada punto al tratarse de routers y placas de red distintas).*

**Análisis de la salida:**
- **Entrada del gateway:** En la captura de la caché de este equipo se observa que la dirección IP del router/gateway local (`192.168.1.1`), alcanzable mediante la interfaz de red inalámbrica `wlxb0a7b9df24fd`, se encuentra aprendida y asociada a su dirección física MAC correspondiente en estado `REACHABLE`.
- **Relación con el comportamiento observado en el punto anterior:** Aunque los valores físicos cambien entre redes hogareñas, el principio fundamental de funcionamiento se cumple de forma idéntica: la dirección MAC de destino con la que se encapsula cualquier paquete dirigido fuera de la red local (como un ping hacia una IP de Internet) siempre **coincide exactamente con la dirección MAC física del gateway** registrada en la caché ARP del equipo que emite la comunicación, ya que es el router local quien se encarga de recibir físicamente la trama y reenviarla al exterior.

### f) Generación y captura de tráfico ARP

En este punto se capturó tráfico en la interfaz de red inalámbrica (`wlxb0a7b9df24fd`) utilizando Wireshark con el filtro de visualización:
```text
arp || icmp
```

Dado que las entradas ARP permanecen guardadas temporalmente en la memoria caché del sistema operativo, se utilizaron diferentes métodos para forzar la emisión y captura de paquetes ARP.

#### 1. Generación de tráfico ARP

##### Opción A: Ping a una IP inexistente de la misma subred (192.168.1.253)

Se ejecutó una prueba de conectividad hacia una dirección IP que no está asignada a ningún dispositivo dentro de la red local:

```bash
ping -4 -c 2 192.168.1.253
```

**Resultado en la terminal:**

![Terminal Opción A](./images/punto%202/opcionA_terminal.png)

Como se observa, el sistema operativo intentó resolver la dirección física del destino y, al no obtener respuesta, devolvió el error `Destination Host Unreachable` con un 100% de pérdida de paquetes.

**Captura en Wireshark:**

![Wireshark Opción A](./images/punto%202/opcionA_wireshark.png)

**Análisis de la captura:**
- El equipo emisor (`TPLink_df:24:fd` / `192.168.1.5`) generó exactamente **3 solicitudes consecutivas de ARP Request** en modo difusión (*broadcast*) preguntando:  
  *«Who has 192.168.1.253? Tell 192.168.1.5»* (Tramas 61628, 61711 y 61835).
- **No se registró ningún ARP Reply**, ya que ningún dispositivo en la LAN posee dicha dirección IP asignada.
- **No se transmitió ningún paquete ICMP Echo Request** por la red física. Debido a que el sistema operativo no logró resolver la dirección MAC de destino mediante ARP, la capa de enlace no pudo encapsular la trama Ethernet y el datagrama IP fue descartado localmente.

##### Opción B: Eliminación de la entrada del gateway en la caché y ping al router

Para observar un ciclo completo de resolución de direcciones (Request y Reply exitosos), se eliminó la entrada del router de la caché ARP local y posteriormente se realizó un ping hacia el gateway:

```bash
sudo ip neigh del 192.168.1.1 dev wlxb0a7b9df24fd
ping -4 -c 2 192.168.1.1
```

**Resultado en la terminal:**

![Terminal Opción B](./images/punto%202/opcionB_terminal.png)

El comando ping respondió de forma exitosa (2 paquetes transmitidos, 2 recibidos, 0% de pérdida de paquetes y tiempos de respuesta de 2 a 6 ms).

**Captura en Wireshark:**

![Wireshark Opción B](./images/punto%202/opcionB_wireshark.png)

**Análisis de la captura:**
- Inmediatamente después de vaciar la entrada del gateway con `ip neigh del`, el sistema operativo generó un **ARP Request** en difusión (*broadcast*) a la dirección `ff:ff:ff:ff:ff:ff` preguntando por la MAC del router:  
  *«Who has 192.168.1.1? Tell 192.168.1.5»* (Trama 3969).
- El router respondió inmediatamente con un **ARP Reply** en modo monodifusión (*unicast*) directo a la MAC de la PC:  
  *«192.168.1.1 is at c4:34:5b:17:17:5d»* (Trama 3970).
- Una vez actualizada la caché ARP, los paquetes de ping (**ICMP Echo Request** y **ICMP Echo Reply**) se transmitieron con normalidad (Tramas 82422 a 82557) sin requerir nuevas consultas ARP.

##### Opción C: Comunicación con otro equipo de la LAN (192.168.1.3)

Se realizó una prueba de comunicación directa hacia otro host ubicado dentro de la misma red de área local (en este caso, un dispositivo con dirección IP `192.168.1.3`), eliminando previamente su entrada de la caché para forzar el ciclo ARP:

```bash
sudo ip neigh del 192.168.1.3 dev wlxb0a7b9df24fd
ping -4 -c 2 192.168.1.3
```

**Resultado en la terminal:**

![Terminal Opción C](./images/punto%202/opcionC_terminal.png)

El comando ping respondió exitosamente (2 paquetes transmitidos, 2 recibidos, 0% de pérdida de paquetes).

**Captura en Wireshark:**

![Wireshark Opción C](./images/punto%202/opcionC_wireshark.png)

**Análisis de la captura:**
- **Resolución directa entre hosts de la LAN:** El equipo emisor (`192.168.1.5` / `TPLink_df:24:fd`) transmitió un **ARP Request** en difusión (*broadcast*) preguntando:  
  *«Who has 192.168.1.3? Tell 192.168.1.5»* (Trama 1870).  
  El host destinatario contestó con un **ARP Reply** en monodifusión (*unicast*) informando su dirección física:  
  *«192.168.1.3 is at 6c:05:d3:58:24:fd»* (Trama 1885).
- **Tráfico directo entre estaciones:** A diferencia de las comunicaciones hacia Internet (donde la MAC de destino es obligatoriamente la del router), en este caso las tramas Ethernet viajan directamente entre las placas de red físicas de ambos equipos (`b0:a7:b9:df:24:fd` y `6c:05:d3:58:24:fd`), actuando el router únicamente como conmutador (*switch*) de capa 2.
- **Impacto de ARP en la latencia (RTT):** En la terminal se evidencia que el primer paquete ICMP tuvo un tiempo de respuesta de **218 ms**, mientras que el segundo paquete tardó solamente **3.67 ms**. Esta marcada diferencia ilustra perfectamente el costo temporal de la resolución: el primer paquete debió esperar a que se complete el intercambio ARP Request/Reply antes de poder ser transmitido, mientras que el segundo se envió inmediatamente consultando la entrada ya aprendida en la caché ARP.

#### 2. Análisis comparativo: ARP Request vs. ARP Reply

Tomando como referencia las tramas obtenidas en la **Opción B** (Trama 3969 de solicitud y Trama 3970 de respuesta), se completó la siguiente tabla con los valores reales observados en Wireshark:

| Campo | ARP Request (Trama 3969) | ARP Reply (Trama 3970) |
| :--- | :--- | :--- |
| **MAC destino (encabezado Ethernet)** | `ff:ff:ff:ff:ff:ff` (Broadcast) | `b0:a7:b9:df:24:fd` (Unicast a la PC) |
| **MAC origen (encabezado Ethernet)** | `b0:a7:b9:df:24:fd` (PC emisora) | `c4:34:5b:17:17:5d` (Router Huawei) |
| **Opcode** | `1` (*request*) | `2` (*reply*) |
| **Sender MAC address** | `b0:a7:b9:df:24:fd` (PC emisora) | `c4:34:5b:17:17:5d` (Router Huawei) |
| **Sender IP address** | `192.168.1.5` | `192.168.1.1` |
| **Target MAC address** | `00:00:00:00:00:00` | `b0:a7:b9:df:24:fd` (PC consultante) |
| **Target IP address** | `192.168.1.1` (IP consultada) | `192.168.1.5` |

#### 3. Preguntas de análisis sobre la captura

##### a)
El **ARP Request** debe enviarse obligatoriamente a la dirección de difusión (*broadcast*, `ff:ff:ff:ff:ff:ff`) porque el equipo emisor desconoce totalmente qué placa física posee la IP buscada; la única forma de asegurar que el mensaje llegue al destinatario es transmitiéndolo a todos los hosts de la red local. Por el contrario, el **ARP Reply** se envía en modo monodifusión (*unicast*) porque el host que responde ya conoce la dirección MAC de quien le preguntó (dado que venía incluida en el campo *Sender MAC address* de la solicitud), evitando generar tráfico innecesario para el resto de la red.

El **Target MAC address** en el Request tiene el valor `00:00:00:00:00:00` debido a que es justamente la información desconocida que se está intentando averiguar mediante la consulta.

##### b)
El **Campo Type (EtherType)** tiene el valor **`0x0806`** (*Address Resolution Protocol*).

No hay encabezado IP. El mensaje ARP se transporta directamente encapsulado dentro de la trama Ethernet.

ARP opera directamente sobre la **Capa de Enlace de Datos (Capa 2)**, funcionando a la par de IP (no viaja dentro de IP como lo hace ICMP), sirviendo como protocolo de control y traducción entre el direccionamiento lógico de red (IPv4) y el direccionamiento físico (Ethernet).

##### c)
Aparecieron **3 solicitudes ARP Request** consecutivas (reintentos generados por el kernel de Linux).

**No hubo ningún ARP Reply**, ya que ningún dispositivo de la red posee la dirección `192.168.1.253`.

No apareció ningún paquete ICMP Echo Request en la red. Esto ocurre porque, para que el sistema operativo pueda enviar el datagrama IP a nivel físico, necesita indefectiblemente conocer la dirección MAC de destino para construir la cabecera Ethernet. Al fallar la resolución ARP y expirar el tiempo de espera, la trama nunca se puede armar y el paquete es descartado internamente por el sistema operativo, notificando en consola `Destination Host Unreachable`.

##### d)
Al realizar un nuevo ping poco tiempo después, **no vuelve a generarse tráfico ARP en la red**. El sistema operativo consulta su caché ARP local, encuentra la correspondencia IP-MAC previamente aprendida y transmite las tramas Ethernet directamente.

Optimiza el rendimiento global de la red al evitar la sobrecarga y congestión por tramas de difusión (*broadcast storms*), eliminando el retardo o latencia que conllevaría resolver la dirección física antes de cada transmisión individual.

Si un equipo cambia de placa de red, se apaga, o el servidor DHCP reasigna esa dirección IP a otro dispositivo (o ante un ataque de *ARP Spoofing*), el emisor seguirá enviando tramas dirigidas a una dirección física incorrecta o inexistente, provocando la pérdida total de conectividad con ese host o permitiendo la interceptación de tráfico hasta que la entrada expire y sea renovada dinámicamente.

---

## 3) TCP y UDP "a mano" con ncat

## Conceptos previos:

### a) Establecimiento de conexión

 Establecer una conexión significa que dos extremos (computadoras o servidores) realizan un acuerdo (handshake) para sincronizar parametros antes de enviar datos. Esta conexión existe de forma lógica únicamente entre los sistemas operativos de los extremos, por tanto, cualquier intermediario como cables o routers solo enrutan paquetes sin conocer este estado.

 ### b) Puertos e identificación

Un puerto es un identificador lógico que dirige el tráfico hacia una aplicación específica. El par (IP, puerto) identifica de manera unica a un proceso particular ejecutándose en una máquina determinada dentro de la red.

### c) Procesos en escucha

Significa que el programa está activo, asociado a un puerto local por el sistema operativo, y a la espera de recibir peticiones de conexión entrantes.

## Análisis de capturas:

### a) Sincronización inicial

Al ejecutar el comando del cliente TCP, se generaron tres paquetes de control automáticamente antes de escribir ningún mensaje. Esto corresponde al Three-Way Handshake (sincronización inicial), donde se observan los segmentos SYN, SYN-ACK y ACK.

![Three-way handshake TCP](./images/punto%203/tcp_handshake.png)

Por el contrario, al ejecutar el cliente UDP no se registró ningún tráfico en la red. No se capturaron paquetes hasta que se envió el primer mensaje de texto, demostrando que UDP no establece una conexión previa.

*(Nota: Para evidenciar esto de forma limpia, la captura UDP se realizó utilizando el puerto 12005. Wireshark mapea por defecto los puertos 12000 al 12004 al protocolo LLC, lo que ocultaba la capa UDP pura en la visualización predeterminada).*

![Primer mensaje UDP](./images/punto%203/udp_nohandshake.png)

### b) Confirmación de entrega

En la comunicación UDP, cada mensaje enviado generó exactamente un datagrama. En este protocolo no existe un mecanismo de confirmación como (ACK) a nivel de transporte; no hay confirmaciones automáticas de llegada.

![Mensaje UDP sin ACK](./images/punto%203/udp_nohandshake.png)

Por el contrario, en TCP, cada mensaje enviado genera al menos dos segmentos en la red: el paquete que transporta los datos (generalmente con las banderas PSH, ACK) y la respuesta automática del receptor confirmando su recepción mediante un segmento ACK.

![Mensaje TCP con ACK](./images/punto%203/tcp_mensaje.png)

### c) Tamaño de cabeceras

Al inspeccionar los detalles del paquete TCP, la cabecera tiene un tamaño de 32 bytes (20 bytes de base más 12 bytes de opciones).

![Cabecera TCP](./images/punto%203/tcp_header.png)

Por su parte, la cabecera UDP ocupa únicamente 8 bytes. Esta gran diferencia de tamaño se debe a la cantidad de información de control que maneja cada protocolo. Mientras que UDP es un protocolo simple que solo incluye puertos, longitud y suma de comprobación, TCP necesita campos adicionales para garantizar la entrega, el orden de los paquetes, el control de flujo y el manejo de la ventana de transmisión.

![Cabecera UDP](./images/punto%203/udp_header.png)

### d) Cierre de conexión

Al interrumpir el programa en TCP, se registró un intercambio de paquetes de control en la red para finalizar la sesión de manera ordenada. Se generaron segmentos con las banderas `[FIN, ACK]` indicando el fin de la transmisión, seguidos de las confirmaciones `[ACK]`.

![FIN tcp](./images/punto%203/tcp_cierre.png)

Por el contrario, al cerrar el cliente UDP, Wireshark no detectó ningún tráfico adicional. Por su naturaleza no orientada a la conexión, UDP simplemente interrumpe la emisión de datagramas sin enviar notificaciones al receptor ni coordinar un cierre.

![FIN udp](./images/punto%203/udp_cierre.png)

### e) Confiabilidad y paquetes extra

Para enviar una frase, UDP requirió unicamente un paquete, el datagrama con los datos. TCP requirió un mínimo de dos paquetes en el intercambio directo, el segmento con los datos y el segmento ACK de respuesta, además del costo fijo de tres paquetes iniciales para establecer la conexión. 

Con los paquetes extra en TCP se "compra" confiabilidad. El mecanismo de acuse de recibo `[ACK]` garantiza que el mensaje llegó a destino. Además, la cabecera más robusta asegura el orden de entrega, evita duplicados y maneja el control de flujo y congestión.

### f) Rechazo de conexión

Al intentar comunicarse sin servidores a la escucha, el sistema operativo rechaza activamente el tráfico de ambos protocolos utilizando mecanismos diferentes:
* **En TCP**, el cliente envía un paquete de sincronización (SYN) y el sistema responde inmediatamente con un segmento que contiene la bandera `[RST, ACK]` (Reset), abortando la conexión.
* **En UDP**, el cliente envía el datagrama a la red, pero al no haber un proceso escuchando en ese puerto, el sistema operativo responde enviando un mensaje de error a través del protocolo ICMP con el código "Destination unreachable (Port unreachable)".

![Rechazo de conexion sin servidores](./images/punto%203/puertos_cerrados.png)

---

## 4) Servidor TCP - TP5 Redes de Computadoras

Para realizar este punto, utilizamos los script recomendados en la guia, en dos consolas se ejecuto el script del servidor y del cliente.

![Server](<images/punto 3/Punto4_Server.png>)

![Cliente](<images/punto 3/Punto4_Cliente.png>)

Para capturar y analizar el tráfico de red utilizamos Wireshark con el filtro "tcp.port == 12000".

![Wireshark](<images/punto 3/Punto4_Wireshark.png>)

Y con la informacion se completa siguiente tabla

| Llamada | ¿Dónde se ejecuta? | ¿Genera tráfico? | Segmentos que  observan |
| :--- | :--- | :--- | :--- |
| **`socket()`** | Servidor y cliente | **No** | **Ninguno**. Llamada local en el sistema operativo para instanciar la estructura de datos del socket. |
| **`bind()`** | Servidor | **No** | **Ninguno**. Asocia localmente la IP `127.0.0.1` y el puerto `12000` al socket del servidor. |
| **`listen()`** | Servidor | **No** | **Ninguno**. Configura el socket en modo pasivo en el SO para escuchar conexiones. |
| **`connect()`** | Cliente | **Sí** | **Three-Way Handshake (Establecimiento de conexión)**:<br>• **Pkt 1:** `47272 → 12000 [SYN] Seq=0 Win=65495 Len=0`<br>• **Pkt 2:** `12000 → 47272 [SYN, ACK] Seq=0 Ack=1 Win=65483 Len=0`<br>• **Pkt 3:** `47272 → 12000 [ACK] Seq=1 Ack=1 Win=65536 Len=0` |
| **`accept()`** | Servidor | **No**  | **Ninguno**. La función está bloqueada en la aplicación a la espera de conexiones; retorna cuando el SO completa el *handshake* tras procesar el Pkt 3. |
| **`sendall()`** | Servidor  | **Sí** | **Envío de datos de aplicación**:<br>• **Pkt 4:** `47272 → 12000 [PSH, ACK] Seq=1 Ack=1 Len=22` (Cliente envía mensaje)<br>• **Pkt 6:** `12000 → 47272 [PSH, ACK] Seq=1 Ack=23 Len=32` (Servidor envía respuesta) |
| **`recv()`** | Servidor  | **No**  | **Ninguno**. Lee los datos acumulados en el buffer del SO. La pila TCP del sistema operativo genera los ACK automáticamente:<br>• **Pkt 5:** `12000 → 47272 [ACK] Seq=1 Ack=23` (Servidor confirma Pkt 4)<br>• **Pkt 7:** `47272 → 12000 [ACK] Seq=23 Ack=33` (Cliente confirma Pkt 6) |
| **`close()`** | Ambos | **Sí** | **Cierre ordenado de la conexión**:<br>• **Pkt 8:** `47272 → 12000 [FIN, ACK] Seq=23 Ack=33` (Cliente inicia cierre)<br>• **Pkt 9:** `12000 → 47272 [FIN, ACK] Seq=33 Ack=24` (Servidor responde con FIN/ACK)<br>• **Pkt 10:** `47272 → 12000 [ACK] Seq=24 Ack=34` (Confirmación final del cliente) |