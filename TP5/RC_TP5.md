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

2. 

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




## Tabla con detalles de detalles del echo Request realizado 
<img src="./images/punto%201/tabla.jpg">

--- 


### a)Direccion MAC y comparacion frente a IP


MAC destino de echo a 8.8.8.8


<img src="./images/punto%201/macDeEcho.png">

mac destino de gateway

<img src="./images/punto%201/macDeGateway.jpg">

La MAC destino del Echo Request enviado a 8.8.8.8, ¿es la MAC de 8.8.8.8? ¿De qué equipo es?

La direccion MAC no es de 8.8.8.8 sino que es la direccion mac del router que esta en la misma LAN.

 

Comparando las macs de destino en ambos pings vemos que es la misma.

¿Qué conclusión se saca sobre el alcance de una dirección MAC frente al de una dirección IP?

 La conclusión es que el alcance de las macs es reducido a redes locales, a diferencia de las ips que pueden viajar por fuera de la red local a internet.

### b) Comparacion del request y reply de echo
veremos los campos que cambian en ethernet, IP e ICMP
#### En ethernet:
* se puede ver que se intercambiaron las direcciones mac de destino y origen en el request como en el reply


request


<img src="./images/punto%201/ethernet-1.png">


reply

<img src="./images/punto%201/ethernet-2.png">

#### En IP se cambiaron:

* la ip origen es la de echo request y la destino es la del gateway

* los valores de identificación

* el time to live es el doble en el request que en el reply

* el header checksum cambio

* el source addres y destination address se intercambiaron en echo y reply


request

<img src="./images/punto%201/IP-1.png">

reply

<img src="./images/punto%201/IP-2.png">

#### En ICMP se cambiaron:

•	cambiaron el campo type

•	cambio el checksum 

•	el request tiene un campo response frame

•	el reply tiene un campo request frame con el valor de response frame mas uno de request

•	reply tiene un campo response time

request

<img src="./images/punto%201/ICMP-1.png">

reply

<img src="./images/punto%201/ICMP-2.png">

¿Por qué tienen sentido estos cambios?

Los cambios tienen sentido porque los paquetes viajan hacia el router y deben retornar al origen, por lo cual deben revertirse algunos campos para realizar el camino inverso de vuelta

¿Por qué el identificador y el número de secuencia se mantienen?

El identificador y el número de secuencia se mantienen porque permite la vinculación univoca de cada reply a su request respectiva

   ### c) Sobre el payload:


El payload esta al final de las secuencias de los paquetes, tiene 32 bytes y contiene el abecedario y un hi

payload:
<img src="./images/punto%201/payload.png">
(es el mismo tanto en reply como request)

Respecto a las diferencias entre como manejan el payload un SO windows  linux:

La diferencia entre una pc con Windows y Linux muestra que la implementación del comando ping y la carga útil dependen del sistema operativo, aunque el estándar ICMP funciona igual a nivel de red

### d) Sobre el TTL:

El valor de TTL en echo del request a 8.8.8.8 es de 128 y en el reply es de 117.

No son iguales porque cada vez que un paquete ip atraviesa un router, el router decrementa el valor del campo TTL en almenos una unidad antes de reenviarlo y como el paquete viajo por varios routers desde internet a la computadora de vuelta el TTL se reduce en el regreso

### e) Representacion del encapsulamiento como "cajas dentro de cajas" del paquete


<img src="./images/punto%201/encapsulamiento.png">
---continuacion punto 2---




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