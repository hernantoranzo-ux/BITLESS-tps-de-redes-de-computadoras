<div align="center">

<img src="./images/image1.png" width="200" alt="Logo UNC" />

# Universidad Nacional de Córdoba

**Facultad de Ciencias Exactas, Físicas y Naturales**
**Cátedra Redes de Computadoras**

## Trabajo Práctico 7

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

- [Parte Práctica](#parte-práctica)
  - [4) Verificar la integridad del mensaje](#4-verificar-la-integridad-del-mensaje)
  - [5) Ubicar y corregir los errores](#5-ubicar-y-corregir-los-errores)
  - [6) Decodificar el mensaje corregido](#6-decodificar-el-mensaje-corregido)
  - [7) Comparar paridades con script propio](#7-comparar-paridades-con-script-propio)
- [Reflexiones e investigación](#reflexiones-e-investigación)
  - [2) Contexto en el mensaje](#2-contexto-en-el-mensaje)
  - [3) Confirmar la corrección](#3-confirmar-la-corrección)

---

## Consignas

## Parte Práctica

### 4) Verificar la integridad del mensaje

Teniendo en cuenta que el mensaje recibido corresponde al **1° Payload** y que este en el campo `"SIZE"` nos indica que debemos armar una matriz 8x7 (8 filas, 7 columnas) con el mismo, organizándolo e ingresándolo en **decodificar_mensaje.py** se observa lo siguiente (prueba realizada en **Colab**):

<img src="./images/parte_practica/decodificar_mensaje.png">

Reorganizando el **Payload**, lo obtenido es: **https://youtube.com/shorts/q8wgKzJwPa$?featurE=share**, un enlace que apunta a Youtube pero parece estar erróneo.

Para comprobar esto, utilizamos el archivo **paridad.py**:

<img src="./images/parte_practica/paridad.png">

De acuerdo a dicho script, la paridad obtenida es **01110011 0110111101110110111100001111001100001100110010001** (La separación corresponde a la paridad de fila y la paridad de columna). Mientras que los bits de control son **01111101 0110111111110110011100101111001100001100110010001**. Colocando ambos juntos para comparar:

01110011 0110111101110110111100001111001100001100110010001 -> **Bits de paridad calculados**  
01111101 0110111111110110011100101111001100001100110010001 -> **Bits de control recibidos**

Comparando ambos, vemos qué filas y qué columnas fallan (el síndrome):
- **Filas que fallan:** Fila 4, Fila 5 y Fila 6.
- **Columnas que fallan:** Columna 8, Columna 16 y Columna 22.

### 5) Ubicar y corregir los errores

Los errores encontrados en los cruces de esas filas y columnas son los siguientes (teniendo en cuenta que contamos las filas y columnas partiendo desde 0):
- **Fila 4, columna 8** -> Hay un **1**, debería ser un **0**.
- **Fila 5, columna 16** -> Hay un **0**, debería ser un **1**.
- **Fila 6, columna 22** -> Hay un **0**, debería ser un **1**.

Cambiando esos bits del **Payload** obtenido, el resultado es el siguiente:

```text
1101000111010011101001110000111001101110100101111
0101111111100111011111110101111010011101011100010
1100101010111011000111101111110110101011111110011
1101000110111111100101110100111001101011111110001
0111000101011111001111001011111101010010101110111
1010000110000101101000111111110011011001011100001
1110100111010111100101100101011110111100111101000
1100001111001011001010100000010000001000000100000
```

### 6) Decodificar el mensaje corregido

Con los datos ya corregidos armamos el archivo de texto y, colocándolo nuevamente en el **script de decodificación**:

<img src="./images/parte_practica/decodificacion_correcta.png">

Lo obtenido es, armando bien el link: **https://youtube.com/shorts/q8WgKzJwPa4?feature=share**. Este nos lleva al video correspondiente al **premio 1**:

<img src="./images/parte_practica/premio_1.png">

Acá se nota lo que advertía la consigna de que un mensaje que "se lee bien" no es necesariamente correcto: al principio parecía que decía YouTube pero con caracteres cambiados como `Pa$` o `featurE` no funcionaba, y recién al corregir los bits con la paridad pudimos llegar al video real.

### 7) Comparar paridades con script propio

Como `paridad.py` solamente calcula las paridades y no las compara con las recibidas en el paquete, nos basamos en ese script para armar uno propio (`comparar_paridad.py`) que tome el archivo del payload (con `"SIZE"`, `"PL"` y `"CHECK"`) y haga la comparación de forma automática:

```python
import sys
import json

ruta = sys.argv[1] if len(sys.argv) > 1 else "payload1.json"

with open(ruta, encoding="utf-8") as f:
    data = json.load(f)

filas_n, cols_chars = map(int, data["SIZE"].split(","))
cols_bits = cols_chars * 7
pl = data["PL"]
check = data["CHECK"]

# Separamos el payload en filas de bits
filas = [pl[i * cols_bits:(i + 1) * cols_bits] for i in range(filas_n)]
check_filas = check[:filas_n]
check_cols = check[filas_n:]

# 1. Comparar paridad de cada fila
for i, fila in enumerate(filas):
    calc = fila.count("1") % 2
    ctrl = int(check_filas[i])
    estado = "[OK]" if calc == ctrl else "[ERROR]"
    print(f"{fila} | Calc: {calc} Ctrl: {ctrl} {estado}")

print("-" * 80)

# 2. Comparar paridad de cada columna
calc_cols = "".join(str(sum(fila[j] == "1" for fila in filas) % 2) for j in range(cols_bits))
print(f"{calc_cols} | Paridad Columna Calculada")
print(f"{check_cols} | Paridad Columna Control (CHECK)")

# 3. Marcar las columnas con discrepancias
discrepancias = "".join("^" if calc_cols[j] != check_cols[j] else " " for j in range(cols_bits))
print(f"{discrepancias} | Discrepancias marcadas con ^")
```

Al ejecutarlo, el resultado que obtuvimos es el siguiente:

<img src="./images/parte_practica/script_control_paridad.png">

Como se observa, el script compara automáticamente cada fila calculada contra el control marcando `[ERROR]` en las filas 4, 5 y 6, y abajo compara la paridad de columnas colocando el puntero `^` en las columnas 8, 16 y 22. De esta forma pudimos verificar y ubicar al instante los bits con error sin tener que revisar los 57 bits a mano uno por uno.

---

## Reflexiones e investigación

### 2) Contexto en el mensaje

Cuando leemos un texto en castellano, es fácil identificar un error de tipeo o de transmisión porque el contexto y las reglas del idioma nos permiten anticipar qué iba en ese lugar. Sin embargo, ese criterio no sirve para datos como una contraseña, un número de cuenta bancaria o una dirección web ya que no existe un contexto ni reglas de lenguaje para adivinar qué carácter falta o cuál se alteró. Cada símbolo es arbitrario y exacto, un solo bit cambiado hace que la contraseña sea incorrecta, una transferencia vaya a otra cuenta o que el navegador intente conectarse a una dirección distinta o inexistente.

Frente a "leer y adivinar", el código de detección y corrección de errores aporta un método matemático automatizable que no depende de la interpretación de una persona. Al organizar los datos en una matriz y agregar bits de paridad par mediante una operación XOR para cada fila y cada columna, el receptor puede comprobar si los datos sufrieron alteraciones en el canal. Además de avisar con certeza si hubo un fallo simple, la paridad bidimensional permite cruzar la fila y la columna que no cierran (el síndrome) para ubicar la posición exacta del bit dañado e invertirlo. En una difusión como la del laboratorio, esto es importante porque nos permite reconstruir el mensaje original por nuestra cuenta sin necesidad de pedirle al transmisor que lo reenvíe.

### 3) Confirmar la corrección

Pudimos confirmar que la corrección fue correcta combinando la validación de las paridades con la prueba funcional del mensaje decodificado. En primer lugar, luego de detectar con nuestro script `comparar_paridad.py` que fallaban las filas 4, 5 y 6 y las columnas 8, 16 y 22 respecto del campo `"CHECK"`, invertimos los bits en las coordenadas (4, 8), (5, 16) y (6, 22). Al volver a calcular la paridad sobre la matriz corregida, verificamos que todas las filas y columnas pasaban a coincidir con los 57 bits de control recibidos, dejando el síndrome en cero.

En segundo lugar, armamos el archivo de texto con los datos corregidos y lo pasamos por `decodificar_mensaje.py` para traducir los bloques de 7 bits a ASCII, obteniendo el enlace `https://youtube.com/shorts/q8WgKzJwPa4?feature=share`. Como indica la guía, que un mensaje se lea bien no asegura que esté libre de errores: además de las fallas visibles en `Pa$` y `featurE`, el error en (4, 8) había cambiado la `W` del identificador por una `w` minúscula, algo imposible de notar a simple vista, sumado a que con tres filas y tres columnas en falla existe más de una combinación de cruces que satisface la paridad. Por eso, la confirmación definitiva fue probar la URL corregida en el navegador y comprobar que abría el video del premio 1.