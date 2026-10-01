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
