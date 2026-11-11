<div align="center">

<img src="./images/image1.png" width="200" alt="Logo UNC" />

# Universidad Nacional de CÃ³rdoba

**Facultad de Ciencias Exactas, FÃ­sicas y Naturales**
**CÃ¡tedra Redes de Computadoras**

## Trabajo PrÃ¡ctico 6

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

## Ãndice

1. [Parte 1: Taller de armado y verificaciÃ³n de cables Cat5/Cat5e bajo estÃ¡ndar T568A/B.](#parte-1-taller-de-armado-y-verificaciÃ³n-de-cables-cat5cat5e-bajo-estÃ¡ndar-t568ab)
   - [1) Pasos para el crimpado de un cable bajo la norma T568A/B DERECHO](#1-pasos-para-el-crimpado-de-un-cable-bajo-la-norma-t568ab-derecho)
   - [2) Armado del cable de nuestro grupo](#2-armado-del-cable-de-nuestro-grupo)
   - [3 y 4) Intercambio de cables con otro grupo](#3-y-4-intercambio-de-cables-con-otro-grupo)
2. [Desarrollar un pequeÃ±o servidor](#desarrollar-un-pequeÃ±o-servidor)
   - [1) Inicio del Servidor y Transferencia de Archivos](#1-inicio-del-servidor-y-transferencia-de-archivos)
   - [2) Captura y AnÃ¡lisis de TrÃ¡fico](#2-captura-y-anÃ¡lisis-de-trÃ¡fico)

---

## Consignas

## Parte 1: Taller de armado y verificaciÃ³n de cables Cat5/Cat5e bajo estÃ¡ndar T568A/B

### 1) Pasos para el crimpado de un cable bajo la norma T568A/B DERECHO

Las normas **T568A** y **T568B** corresponden a las dos configuraciones normalizadas de terminaciÃ³n y asignaciÃ³n de pines para cables de par trenzado de cuatro pares. Ambos estÃ¡ndares provienen de la norma **ANSI/TIA-568** y definen el orden en que deben colocarse los 8 cables de par trenzado en el conector **RJ-45** (mirando el conector con la pestaÃ±a hacia abajo y los contactos dorados hacia arriba, de izquierda a derecha). La Ãºnica diferencia entre **T568A** y **T568B** se da en que se intercambian de posiciÃ³n los pares verde y naranja (los pares azul y marrÃ³n quedan en las mismas posiciones en ambas normas).
El hecho que el cable de red sea derecho hace referencia a usar el mismo estÃ¡ndar de terminaciÃ³n en ambos extremos (es decir, extremo 1 con **T568A** y extremo 2 con **T568A**, o bien ambos extremos con **T568B**). De esta forma, el pin 1 de un extremo viaja directamente al pin 1 del otro, el pin 2 al pin 2, y asÃ­ sucesivamente.

En nuestro grupo hemos decidido usar la norma T568B. La secuencia de colores para el crimpado del cable es la siguiente:

<img src="./images/parte_1/T568B.png">

Los pasos para el crimpado son (adjuntaremos imÃ¡genes obtenidas de internet, puesto que nos hemos olvidado de sacar fotos del paso a paso):

- Teniendo un cable UTP, cortamos un tramo de entre 1 a 1.5 metros. 
- Hacemos uso de un pelacables para cortar el protector con un margen suficiente para manipular luego los pares (aproximadamente unos 3 cm).

<img src="./images/parte_1/crimpado_1.png">

- Separar los 4 pares trenzados y destrenzarlos hasta el inicio del protector. Aplanar los pares con los dedos y ordenarlos de izquierda a derecha de acuerdo a la norma elegida (imÃ¡genes ilustrativas).

<img src="./images/parte_1/crimpado_2.png">
<img src="./images/parte_1/crimpado_3.png">

- En caso de quedar "hilos" sobresalientes, recortarlos.
- Con los pares aplanados y ordenados, los colocamos cuidadosamente (comprobando que ninguno se doble o cruce) dentro del conector **RJ-45**, con la pestaÃ±a plÃ¡stica hacia abajo y los contactos metÃ¡licos dorados mirando hacia arriba. Debemos asegurarnos de empujarlos hasta que estos toquen el frente del **RJ-45**.

<img src="./images/parte_1/crimpado_4.png">

- Usando una crimpeadora, colocamos el conector en la cavidad 8P de la misma y apretamos firmemente hasta que el conector quede completamente crimpeado (Las cuchillas doradas perforan el protector de los pares y la pestaÃ±a plÃ¡stica los apretarÃ¡).

<img src="./images/parte_1/crimpado_5.png">

- Comprobar visualmente que los cables estÃ©n bien alineados.
- Repetir los pasos con el otro extremo del cable aplicando la misma norma (T568B en nuestro caso).
- Ahora, para verificar que el cable estÃ© correctamente crimpeado, utilizamos un Tester de cable Ethernet. El Master deberÃ­a encender las luces secuencialmente para indicar que cada uno de los pines funciona correctamente.

<img src="./images/parte_1/crimpado_6.png">

### 2) Armado del cable de nuestro grupo

Respetando los pasos anteriores, los resultados obtenidos fueron los siguientes:

<img src="./images/parte_1/Bitless_1.jpg" width="600">
<img src="./images/parte_1/Bitless_2.jpg" width="600">
<img src="./images/parte_1/Bitless_3.jpg" width="600">

### 3 y 4) Intercambio de cables con otro grupo

El grupo **Bitless** intercambiÃ³ los cables armados con el grupo **Los simuLANdores**. A continuaciÃ³n brindamos unas imÃ¡genes de lo realizado por ellos.

<img src="./images/parte_1/simuLANdores_1.jpg" width="600">
<img src="./images/parte_1/simuLANdores_2.jpg" width="600">

Como podemos observar a primera vista, ni siquiera han retirado el **hilo de nylon** al realizar el armado del cable. Al menos podrÃ­a haber habido un esfuerzo en cuanto a ocultarlo de alguna manera, pero ha quedado sobresaliente como si hubiera sido lo que se buscara resaltar. Nosotros, como integrantes del grupo **Bitless**, si buscÃ¡ramos **comprar** un **cable Ethernet**, definitivamente **no elegirÃ­amos uno con estos defectos visuales**. Es por ello que adjuntamos la siguiente imagen para indicar el cuidado que deberÃ­an haber tenido a la hora de realizar su armado.

<img src="./images/parte_1/simuLANdores_3.jpg" width="600">

A su vez, para comprobar que su cable funcionara correctamente, utilizamos el **tester de cables Ethernet**. Su cable funcionaba correctamente, por lo que a nivel funcional estaba bien hecho. Sin embargo, el mismo **hilo de nylon** que mencionamos anteriormente **fue una molestia** a la hora de hacer la prueba, pues tenÃ­amos que **apartarlo** para **asegurarnos de que no se introdujera en el tester**. Lamentablemente, nos olvidamos de tomar foto del proceso, por lo que continuaciÃ³n adjuntamos una imagen generada con IA de cÃ³mo fue aproximadamente.

<img src="./images/parte_1/simuLANdores_4.png" width="600">

Aparte de ese **desperfecto imperdonable** que su cable tenÃ­a, los cables se veÃ­an bien alineados y ordenados dentro del conector, por lo que ese proceso se habÃ­a realizado correctamente.

---
## Levantar y testear una LAN
Configuracion manual de la interfaz de red para la actividad

<img src="./images/parte_2/configuracion.png">

### justificacion de las direcciones elegidas

seleccionamos la IP 192.168.1.72 con la mascara de subred 255.255.255.0 porque pertenecen a un rango de direcciones IPv4 privadas ideales para redes de area local (LAN) pequeÃ±as sin salida directa a internet, esto tiene sentido porque la computadora del otro grupo se encontraba en la misma red (192.168.1.100), compartiendo el mismo identificador de red permitiendo la comunicacion atraves del switch fisico sin necesitar un router externo



### Establecimiento del receptor UDP y envio de mensajes intergrupales


<img src="./images/parte_2/receptor_udp.png">

### 5) Preguntas y respuestas sobre UDP

#### a)Â¿Se observa algÃºn procedimiento de establecimiento de conexiÃ³n antes de enviar los datos?


 no, no se observa ningun procedimiento de conexion, la comunicacion udp no es orientada a conexion, osea que los datagramas se envian directamente desde la fuente hacia el destino sin negociar estados previos, intercambiar numeros de secuencia ni sincronizar parametros entre los extremos

#### b)Â¿Existe algÃºn mecanismo de confirmaciÃ³n de recepciÃ³n equivalente a los ACK de TCP?


No a nivel del protocolo UDP, por su caracteristica de no ser confiable, no incorpora mecanismos nativos de control de flujo, retransmision de paquetes perdidos ni acuses de recibo (ACK) 


#### c)Â¿QuÃ© ocurre si se envÃ­an mensajes mientras el receptor UDP no estÃ¡ ejecutÃ¡ndose? 


si no se esta ejecutando un receptor UDP dependiendo de como se configure el SO del destino, el host receptor suele responder con un mensaje de control ICMP port unreachable. Mientras tanto en el emisor, los datagramas se transmiten de todas formas y la terminal no se entera de que nadie lo recibio 


#### d)Â¿UDP garantiza que los mensajes lleguen en el mismo orden en que fueron enviados?

No, cada datagrama se procesa y enruta de manera independiente a traves de la red. Osea que los paquetes pueden tomar rutas distintas, sufrir retardos variables o congestiones puntuales, lo cual hace que lleguen en un orden diferente al que fueron emitidos.

#### e) Â¿QuÃ© aplicaciones reales utilizan UDP y por quÃ©? 

las aplicaciones reales que utilizan UDP son; 
   * streaming de video/audio en tiempo real
   * juegos en linea multijugador 
   * DNS (Domain Name System) 
   * protocolos de red y administracion local(DHCP,TFTO,SNMP)

    En estas aplicaiones se utiliza UDP ya que priorizan la velocidad y baja latencia ademas de que la perdida de paquetes es preferible a sufrir retrasos por retransmisiones y el control de flujo de TCP
---

## Desarrollar un pequeÃ±o servidor

ConfiguraciÃ³n inicial:

IP Computadora 1 (Servidor): 192.168.1.72

IP Computadora 2 (Cliente): 192.168.1.64

`nota: este punto fue realizado en un hogar conectando una notebook y una PC a la red domÃ©stica.`

### 1)  Inicio del Servidor y Transferencia de Archivos:

Para empezar, se creÃ³ un directorio con archivos de prueba y se levantÃ³ un servidor en la Computadora 1 ejecutando el comando `python3 -m http.server 8000`.

![Terminal del servidor](<images/Desarrollar un pequeÃ±o servidor/terminal del servidor.png>)

Luego, desde la Computadora 2, se accediÃ³ a la direcciÃ³n del servidor a travÃ©s de un navegador web, visualizando el Ã­ndice del directorio alojado en el puerto 8000.

![Navegador del cliente](<images/Desarrollar un pequeÃ±o servidor/fotos cliente/navegador del cliente.jpg>)

Se puede observar que durante esta primera etapa de navegaciÃ³n web se realizaron las siguientes peticiones HTTP al servidor para listar el directorio y visualizar/descargar el contenido:

`GET /` Carga del Ã­ndice del directorio, respuesta 200 OK.

`GET /favicon.ico` PeticiÃ³n automÃ¡tica del navegador, respuesta 404 File not found.

`GET /yo.png` Descarga de imagen, respuesta 200 OK.

`GET /TP6_Redes.txt` Descarga de archivo de texto, respuesta 200 OK.

`GET /RC - TP N6.pdf` Descarga de documento PDF, respuesta 200 OK.

Finalmente, se realizÃ³ una nueva transferencia de archivos, pero esta vez solicitando un recurso directamente desde la terminal del cliente utilizando la herramienta curl:

```
curl -O http://192.168.1.72:8000/yo.png
```


![Descarga con cURL](<images/Desarrollar un pequeÃ±o servidor/curl.png>)

### 2) Captura y AnÃ¡lisis de TrÃ¡fico:

Durante todo el proceso de transferencia de los archivos (tanto vÃ­a navegador como por consola), se capturÃ³ el trÃ¡fico en la interfaz de red de la Computadora 2 (wlan0), aplicando un filtro para aislar las comunicaciones salientes y entrantes en el puerto 8000.

![TrÃ¡fico de Wireshark](<images/Desarrollar un pequeÃ±o servidor/trafico wireshark.png>)

Al analizar los paquetes capturados en Wireshark, podemos ver:

TCP: Al inicio de cada solicitud, el establecimiento de una conexiÃ³n mediante el Three-way handshake de TCP.

HTTP: Una vez establecida la conexiÃ³n en la capa de transporte, fluye el trÃ¡fico de aplicaciÃ³n. El cliente realiza peticiones solicitando los recursos con el mÃ©todo GET, por ejemplo, `GET /TP6_Redes.txt HTTP/1.1` y las correspondientes respuestas del servidor. Estas respuestas incluyen un cÃ³digo de estado, siendo `200 OK` cuando se entregÃ³ el recurso con Ã©xito, o `404 File not found` cuando el recurso no existÃ­a como sucediÃ³ con el Ã­cono del navegador.

Podemos observar ademÃ¡s que, al utilizar el protocolo HTTP estÃ¡ndar, la transferencia de los archivos y los encabezados se realiza en texto plano y puede leerse directamente con Wireshark.