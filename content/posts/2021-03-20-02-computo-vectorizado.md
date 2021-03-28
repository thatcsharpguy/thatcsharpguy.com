---
layout: post
title: "02: Cómputo vectorizado"
date: 2021-03-20 14:00:00
summary: "El tema central de esta sesión es la introducción al cómputo vectorizado (ojo, ¡no vectorial!). Nosotros vamos a conocer bajo este término a la práctica de escribir código que se aplique a múltiples elementos de un arreglo de forma simultanea."
image: http://i3.ytimg.com/vi/iLIQykiASs4/maxresdefault.jpg
tags: data fundamentals, data science, machine learning
---

<table style="margin:0; max-width: 1000px;">
    <tbody>
        <tr>
            <td>
                <a href="https://thatcsharpguy.com">
                    <img src="/assets/general/Sharp@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://twitter.com/io_exception">
                    <img src="/assets/general/Twitter@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://tcsg.dev/discord">
                    <img src="/assets/general/Discord@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://github.com/thatcsharpguy/df">
                    <img src="/assets/general/GitHub@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://youtube.com/thatcsharpguy">
                    <img src="/assets/general/YouTube@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://youtu.be/iLIQykiASs4">
                    <img src="/assets/general/EnVivo@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://twitch.tv/thatcsharpguy">
                    <img src="/assets/general/Twitch@1x.png" />
                </a>
            </td>
        </tr>
    </tbody>
</table>

## Paquetes

- `matplotlib`
- `numpy`

## Tipos de dato en Python

### Tipos base

- Números **enteros** - `int`
- Números **flotantes** - `float`
- Números **complejos** - `complex`
- Cadenas - `str`
- Booleanos - `bool`

### Contenedores

- Listas - `list`
- Tuplas - `tuple`
- Mapas - `dict`
- Conjuntos - `set`

muy importantes pero, no tan interesantes a comparación de...

## Los arreglos nuéricos multidimensionales

> 🗣️ Los arreglos multidimensionales también se conocen como _ndarrays_, _N-dimensional arrays_.

Es un tipo de dato que nos sirve para representar colecciones de números, es un tipo **no nativo** de Python.

### Razones

#### Representación de elementos complejos

Imágenes, videos, canciones, modelos 3d... todos estas cosas se pueden representar como arreglos multidimensionales: una canción es un areglo unidimensional de intensidades de sonido, una imagen es un arreglo bidimensional de valores de brillo, y un vídeo es un areglo tridimensional, dos de las dimensiones son brillos y la otra representa el tiempo, un modelo 3d es in conjunto de puntos en un espacio tridimensional representado como un arreglo multidimensional.

#### Trabajar con "datos científicos"

Usualmente realizamos experimentos científicos repetidamente, obteniendo de ellos una secuencia de valores que podemos representar fácilmente como un arreglo. Otras veces hacemos un solo experimento sobre múltiples sujetos, obtieniendo de ellos una secuencia de valores. ¡A veces hacemos las dos cosas a la vez!

#### Elegancia 🧐

Los arreglos multidimensionales nos permiten aplicar y extender las operaciones que podemos aplicar a números individuales para aplicarselos a todos los elementos del arreglo de forma concisa y fácil de leer; la alternativa es escribir ciclos para aplicar operaciones a distintos elementos de una colección (ver ejemplo).

#### Eficiencia

Los arreglos multidimensionales son relativamente compactos (comparados con una lista, por ejemplo) y almacenan la información de forma muy eficiente, lo cual los hace ideales para lidiar con problemas en los que tenemos que lidiar con grandes cantidades de datos.

## Una instrucción, muchos datos

El tema central de esta sesión es la introducción al **cómputo vectorizado** (ojo, ¡no vectorial!). Nosotros vamos a conocer bajo este término a la práctica de escribir código que se aplique a múltiples elementos de un arreglo de forma simultanea. Esto se también como [SIMD](https://en.wikipedia.org/wiki/SIMD) (_Single Instruction Multiple Data_) y representa una forma de implementar cómputo paralelo restringido a operaciones numéricas sobre arreglos, claro.

### GPUs

Uno de los ejemplos por excelencia de dónde es que ocurren este tipo de operaciones es en las tarjetas gráficas, hace poco mencioné que podemos representar modelos 3D usando arreglos multidimensionales; para hacer que estos objetos se muevan mediante rotaciones, traslaciones y demás, debemos aplicar sobre ellos operaciones matemáticas y para eso es que se ocupan este tipo de tarjetas.

Estas tarjetas están especialmente diseñadas (y programadas) para lidiar de manera eficiente con arreglos numéricos y múltiples operaciones que podemos usar para manipularlos. Es por eso que son muy importantes en el mundo de los videojuegos; pero no solo ahí, puesto que cualquier operacion que representemos como arreglos puede ser llevada a cabo en una GPU.

Además de GPUs, Google creó algo llamado **TPUs** (_Tensor Processor Unit_) que es una versión orientada a la manipulación de arreglos multidimensionales para su framework de Deep Learning Tensorflow.

## Las cosas por su nombre

Hasta el momento hemos estado hablando de "arreglos multidimensionales", pero existen nombres específicos para arreglos con 1, 2 y 3 o más dimensiones:

<img src="/assets/02/vector-tensor-matriz.jpg" />

## Propiedades de los arreglos

- Tamaño (siempre "rectangulares")
- Tipo

#### Ejemplo 1

```text
 [0 0 0]
 [0 0 0]
 [0 0 0]
```

**Tamaño**: (3, 3), **tipo**: enteros

#### Ejemplo 2

```text
 [1.3 2.3 3.5 4.1 5.9]
```

**Tamaño**: (5,), **tipo**: float

#### Ejemplo 3 ❌

```text
 [1.3 2.3 3.5]
 [0.0 3.5]
 [1.3 2.3 3.5 1.1]
```

**Tamaño**: (????), **tipo**: float

Como ya mencioné anteriormente, las listas en Python son _listas de objetos_; por el contrario, los _ndarrays_ almacenan los números como _números_ y de forma contigua en memoria; la biblioteca que nos permite interactuar con estos objetos en Python nos permite interactuar con los arreglos como valores numéricos en Python, pero en realidad no lo son.

La importancia de que los arreglos tengan un tipo uniforme para todos sus valores tiene que ver con la eficiencia a la hora de almacenarlos; es más fácil almacenar y acceder a ellos si tenemos un tamaño fijo para todos los elementos.

## NumPy

<img
    src="/assets/02/NumPy.svg"
    alt="triangle with all three sides equal"
    width="300" />

> 🚨 [Citing NumPy](https://numpy.org/citing-numpy/)

Un paquete con que permite operar con arreglos. Es la pieza central en [SciPy](https://www.scipy.org/).

Por convención lo verás (casi siempre) como:

```python
import numpy as np
```

```python
import numpy as np
```

Creemos un arreglo...

```python
my_first_array = np.array([0, 1, 2, 3, 4])
print(my_first_array)
```

    [0 1 2 3 4]

## Recordando las propiedades...

```python
my_second_array = np.array([
    [1, 2],
    [3, 4],
    [5, 6]
])
```

### Tipo - `dtype`

Un nombre para _data type_; recuerda: en un _ndarray_ todos los valores son del mismo tipo.

```python
print(my_second_array.dtype)
```

    int64

### Tamaño - `shape`

```python
print(my_second_array.shape)
```

    (3, 2)

El orden dado es (_filas_, _columnas_, _profundidad_, _???_, ...).

$\begin{matrix}
1 & 2  \\
3 & 4  \\
5 & 6  \\
\end{matrix}$

`my_second_array` tiene tres filas, dos columnas.

### Creación de arreglos

#### `np.array`

Una de las formas más básicas para crear arreglos es a partir de información existente, por ejemplo, una lista (o una lista de listas):

```python
my_list = [9.5, 4.5, 6.3]
my_arr = np.array(my_list)
print(my_arr, my_arr.dtype, my_arr.shape)
```

    [9.5 4.5 6.3] float64 (3,)

```python
multi_dimensional = np.array(
    [
        [1, 2, 3],
        [4, 5, 6]
    ]
)
print(multi_dimensional, multi_dimensional.dtype, multi_dimensional.shape)
```

    [[1 2 3]
     [4 5 6]] int64 (2, 3)

> ⚠️ **Sobre copiar arreglos...**  
> Si necesitamos copiar un arreglo es necesario usar nuevamente `np.array`, o `copy` puesto que los _ndarrays_ son mutables y podemos obtener efectos no deseados

```python
# Incorrecto
a1 = np.array([0,1,2])
a2 = a1
a1[0] = 5000
print(a1)
print(a2)
```

    [5000    1    2]
    [5000    1    2]

```python
# Correcto
a1 = np.array([0,1,2])
a2 = a1.copy() # o np.array(a1)
a1[0] = 5000
print(a1)
print(a2)
```

    [5000    1    2]
    [0 1 2]

#### Arreglos vacíos

También es común que querramos crear arreglos "vacíos" o con valores pre-definidos; en lugar de crear un montón de listas llenas de ceros podemos usar `np.zeros`:

#### `np.zeros`

```python
np.zeros((3,))
```

    array([0., 0., 0.])

```python
np.zeros((3,3))
```

    array([[0., 0., 0.],
           [0., 0., 0.],
           [0., 0., 0.]])

```python
np.zeros((1, 2, 3, 4))
```

    array([[[[0., 0., 0., 0.],
             [0., 0., 0., 0.],
             [0., 0., 0., 0.]],

            [[0., 0., 0., 0.],
             [0., 0., 0., 0.],
             [0., 0., 0., 0.]]]])

```python
np.ones((1, 2, 3, 4))
```

    array([[[[1., 1., 1., 1.],
             [1., 1., 1., 1.],
             [1., 1., 1., 1.]],

            [[1., 1., 1., 1.],
             [1., 1., 1., 1.],
             [1., 1., 1., 1.]]]])

#### `np.ones`

```python
np.ones((3,))
```

    array([1., 1., 1.])

#### `np.full`

A veces es útil poder crear un arreglo con un valor _específico_:

```python
np.full((1,2,3), 1.23)
```

    array([[[1.23, 1.23, 1.23],
            [1.23, 1.23, 1.23]]])

#### `np.(...)_like`

También podemos crear arreglos del mismo tamaño que otros, ya sean llenos con `0`, `1` o cualquier otro valor de preferencia:

```python
base = np.array([[0.0, 1], [2, 3]])
np.zeros_like(base)
```

    array([[0., 0.],
           [0., 0.]])

```python
np.ones_like(base)
```

    array([[1., 1.],
           [1., 1.]])

```python
np.full_like(base, np.pi)
```

    array([[3.14159265, 3.14159265],
           [3.14159265, 3.14159265]])

#### Arreglos aleatorios

El aprendizaje automático tiene un componente de aleatoridad, es necesario saber cómo es que nosotros podemos usar esa aleatoridad. NumPy nos permite crear arreglos aleatorios:

```python
np.random.randint(low = 0, high = 10, size = (3, 3))
```

    array([[0, 4, 0],
           [9, 5, 1],
           [8, 6, 0]])

```python
np.random.uniform(low = 1, high = 2, size = (2, 3))
```

    array([[1.98673578, 1.53312485, 1.07910688],
           [1.95945715, 1.7776157 , 1.53298935]])

```python
np.random.normal(loc = 0, scale = 1, size = (2, 3))
```

    array([[-1.15408806, -1.18588095,  0.13855094],
           [-2.39921893, -0.07689017, -1.03094884]])

#### Secuencias

Otra de las herramientas muy útiles dentro de _NumPy_ es la generación de secuencias incrementales.

#### `np.arange`

Una funcionalidad similar a `range` en Python, solo que en lugar de regresar un generador, `np.arange` retorna un _ndarray_:

```python
np.arange(10)
```

    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

```python
np.arange(-10, 10)
```

    array([-10,  -9,  -8,  -7,  -6,  -5,  -4,  -3,  -2,  -1,   0,   1,   2,
             3,   4,   5,   6,   7,   8,   9])

```python
np.arange(start = 1, stop = 11, step = 2)
```

    array([1, 3, 5, 7, 9])

```python
np.arange(start = 11, stop = 1, step = -2)
```

    array([11,  9,  7,  5,  3])

```python
np.arange(start = 1.1, stop = 2.1, step = 0.1)
```

    array([1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7, 1.8, 1.9, 2. ])

> ⚠️ Los límites en `np.arange` son $[start, end)$, es decir, `start` es inclusivo mientras que `end` es exclusivo por default.

#### `np.linspace`

Si bien `np.arange` nos permite usar fracciones para determinar el tamaño del incremento; no es la mejor manera para generar sucesiones fraccionarias entre números, para eso podemos usar `np.linspace`

```python
np.linspace(0, 5, 5)
```

    array([0.  , 1.25, 2.5 , 3.75, 5.  ])

```python
np.linspace(-10, 10, 15)
```

    array([-10.        ,  -8.57142857,  -7.14285714,  -5.71428571,
            -4.28571429,  -2.85714286,  -1.42857143,   0.        ,
             1.42857143,   2.85714286,   4.28571429,   5.71428571,
             7.14285714,   8.57142857,  10.        ])

```python
np.linspace(start = 3.5, stop = 3.6, num = 10)
```

    array([3.5       , 3.51111111, 3.52222222, 3.53333333, 3.54444444,
           3.55555556, 3.56666667, 3.57777778, 3.58888889, 3.6       ])

> ⚠️ A diferencia de `np.arange`, en `np.linspace` los límit4es son $[start, stop]$, es decir, ambos son inclusivos por default.

### Indexación

Como cualquier secuencia en Python, los _ndarrays_ también nos permiten acceder a los valores individuales que los forman usando la notación `[]`:

```python
index_me = np.array([
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
])

print(index_me[0])
```

    [1 2 3]

```python
print(index_me[0][1])
```

    2

Tenemos a nuestra disposición otra sintaxis para realizar indexación múltiple; nos vamos a asistir de la `,`:

```python
print(index_me[0, 1])
```

    2

### _Slicing_

> 🤠 El [slicing](https://docs.python.org/2.3/whatsnew/section-slices.html) en Python es una de las caracterísitcas más interesantes, porque además de permitirnos especificar los puntos de inicio y fin del _slicing_, también nos permite especificar el tamaño del paso a dar.

Los _ndarrays_ también soportan el _slicing_:

```python
index_me[:,:]
```

    array([[1, 2, 3],
           [4, 5, 6],
           [7, 8, 9]])

```python
index_me[:, 0] # Primera columna
```

    array([1, 4, 7])

```python
index_me[:, 1:] # Últimas dos columnas
```

    array([[2, 3],
           [5, 6],
           [8, 9]])

```python
index_me[1:, 1:]
```

    array([[5, 6],
           [8, 9]])

```python
long_array = np.arange(10, 20)

print(long_array)
```

    [10 11 12 13 14 15 16 17 18 19]

```python
print(long_array[2:6])
```

    [12 13 14 15]

```python
print(long_array[2:-2])
```

    [12 13 14 15 16 17]

```python
print(long_array[::3])
```

    [10 13 16 19]

```python
print(long_array[::-1])
```

    [19 18 17 16 15 14 13 12 11 10]

### Asignación de valores

A recordar que los _ndarrays_ son mutables; podemos cambiar los valores de un arreglo usando indexación y _slicing_

```python
arreglo = np.arange(0, 20)

print(arreglo)
```

    [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]

```python
arreglo[0] = -500
print(arreglo)
```

    [-500    1    2    3    4    5    6    7    8    9   10   11   12   13
       14   15   16   17   18   19]

```python
arreglo[::2] = -100
print(arreglo)
```

    [-100    1 -100    3 -100    5 -100    7 -100    9 -100   11 -100   13
     -100   15 -100   17 -100   19]

```python
arreglo[2:5:] = 0
print(arreglo)
```

    [-100    1    0    0    0    5 -100    7 -100    9 -100   11 -100   13
     -100   15 -100   17 -100   19]

### _Playground_

<img src="/assets/02/filip-gielda-VPavA7BBxK0-unsplash.jpg" />

Photo by <a href="https://unsplash.com/@filipovsky?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Filip Gielda</a> on <a href="/s/photos/mexico?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

```python
import matplotlib.pyplot as plt
import matplotlib.image as mpimg

chichen_itza = mpimg.imread('assets/02/filip-gielda-VPavA7BBxK0-unsplash.jpg')
print(type(chichen_itza))
print(chichen_itza.dtype)
print(chichen_itza.shape)

plt.imshow(chichen_itza[
    80:170,
    230:400
])
```

    <class 'numpy.ndarray'>
    uint8
    (427, 640, 3)





    <matplotlib.image.AxesImage at 0x1201c3d30>

![png](02-computo-vectorizado_files/02-computo-vectorizado_76_2.png)

```python
ci_copy = chichen_itza[80:170, 230:400].copy()
ci_copy[::2,::2, :] = 0
plt.imshow(ci_copy)
```

    <matplotlib.image.AxesImage at 0x1202760d0>

![png](02-computo-vectorizado_files/02-computo-vectorizado_77_1.png)

```python
ci_copy = chichen_itza[::4, ::4].copy()
plt.imshow(ci_copy)
```

    <matplotlib.image.AxesImage at 0x1202d9610>

![png](02-computo-vectorizado_files/02-computo-vectorizado_78_1.png)

### Operaciones arreglo-escalar

```python
# Suma
test1 = np.arange(10, 15)
print(test1)
print(test1 + 10)
```

    [10 11 12 13 14]
    [20 21 22 23 24]

```python
# Multiplicaciones
print(test1 * 10)
print(test1 / 10)
```

    [100 110 120 130 140]
    [1.  1.1 1.2 1.3 1.4]

```python
# cos, abs, pow
test2 = np.arange(-5, 5)
print(test2)
print(np.abs(test2))
print(np.cos(test2))
print(np.power(test2, 2))
```

    [-5 -4 -3 -2 -1  0  1  2  3  4]
    [5 4 3 2 1 0 1 2 3 4]
    [ 0.28366219 -0.65364362 -0.9899925  -0.41614684  0.54030231  1.
      0.54030231 -0.41614684 -0.9899925  -0.65364362]
    [25 16  9  4  1  0  1  4  9 16]

### Operaciones elemento-elemento

```python
# Suma
a = np.arange(10, 15)
b = np.arange(20, 25)
print(a, b)
print(a + b)
print(a - b)
```

    [10 11 12 13 14] [20 21 22 23 24]
    [30 32 34 36 38]
    [-10 -10 -10 -10 -10]

```python
# Multiplicación
print(a * b)
print(a / b)
```

    [200 231 264 299 336]
    [0.5        0.52380952 0.54545455 0.56521739 0.58333333]

### _Broadcasting_

Las operaciones elemento a elemento funcionan **siempre y cuando** los arreglos con los que estamos operando sean del mismo tamaño y forma... ¿pero qué sucede cuando no es así?

Aún así podemos realizar operaciones bajo ciertas condiciones en nuestros operandos `x` e `y`:

- Si `x` es un areglo e `y` un escalar, el escalar actúa sobre todos los elementos del arreglo.
- Si `x` e `y` tienen las mismas dimensiones y tamaños, la operación sucede elemento a elemento.
- Si uno de los operandos tiene menos dimensiones que el otro, y las **ultimas dimensiones** del izquierdo coinciden con la forma del operando derecho, las operaciones suceden repitiendo el elemento derecho tantas veces como sea necesario

  - `(2, 2) * (2,)` ✅
  - `(1, 2, 3) * (2, 3)` ✅
  - `(10, 20, 20) * (20,)` ✅
  - `(10, 20, 20) * (10,20)` ❌
  - `(10,) * (20,)` ❌

```python
vector = np.array([1, 2, 3, 4])
unos = np.ones((3, 4))
print(unos.shape, vector.shape)
```

    (3, 4) (4,)

```python
print(unos + vector)
```

    [[2. 3. 4. 5.]
     [2. 3. 4. 5.]
     [2. 3. 4. 5.]]

```python
print(unos / vector)
```

    [[1.         0.5        0.33333333 0.25      ]
     [1.         0.5        0.33333333 0.25      ]
     [1.         0.5        0.33333333 0.25      ]]

### Transposición

Una de las operaciones más comunes sobre matrices es la transposición, _NumPy_ ofrece una sintaxis especial: `array.T`:

```python
matriz = np.array([
    [1, 2, 3, 4],
    [0, 0, 0, 0],
    [1, 2, 3, 4],
])

print(matriz)
print(matriz.T)
```

    [[1 2 3 4]
     [0 0 0 0]
     [1 2 3 4]]
    [[1 0 1]
     [2 0 2]
     [3 0 3]
     [4 0 4]]

### Transformación de arreglos

> ❓❓❓ ¿hacemos otro live opcional?

### Indexación "elegante"

Ya vimos cómo acceder a elementos mediante indexación y _slicing_, pero _NumPy_ permite otro tipo de indexación, conocida como 💅*Fancy Indexing* 🎩.

#### Arreglos como índices

Podemos usar otros arreglos como índices

```python
indexing_array = np.array([3, 2 , 5, 1, 4])
original_array = np.arange(10, 20)

print(original_array[indexing_array])
```

    [13 12 15 11 14]

```python
indexing_array = np.array([0, 0, 0, 0, 1, 2, 2])
print(original_array[indexing_array])
```

    [10 10 10 10 11 12 12]

```python
indexing_array = np.array([0])
print(original_array[indexing_array])
```

    [10]

#### Máscaras - booleanos como índices

También podemos usar arreglos booleanos como índices 🤔

```python
booleans = [True, False, True, False, False]
original_array = np.arange(5)

print(original_array)
print(original_array[booleans])
```

    [0 1 2 3 4]
    [0 2]

```python
original_array = np.arange(22)
even_numbers = original_array % 2 == 0

print(original_array)
print(original_array[even_numbers])
print(original_array[original_array > 10])
```

    [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21]
    [ 0  2  4  6  8 10 12 14 16 18 20]
    [11 12 13 14 15 16 17 18 19 20 21]

### Agregaciones

Siempre es una buena idea echarle un ojo a algunas caracrerísticas de nuestros datos a través de información agregada o sintetizada.

```python
bitcoin_prices = np.array([
    59_295.50, 45_164.00, 33_108.10, 28_949.40, 19_698.10,
    13_797.30, 10_776.10, 11_644.20, 11_333.40,  9_135.40,
     9_454.80,  8_629.00,  6_412.50,  8_543.70,  9_349.10,
     7_196.40,  7_546.60,  9_152.60,  8_284.30,  9_594.40,
    10_082.00, 10_818.60,  8_558.30,  5_320.80,  4_102.30,
])
```

#### Estadística - mean, median, desviación estándar

```python
print(np.mean(bitcoin_prices))
print(np.median(bitcoin_prices))
print(np.std(bitcoin_prices))
```

    14637.875999999998
    9454.8
    13009.079842334122

#### Mínimos y máximos

```python
print(np.max(bitcoin_prices))
print(np.min(bitcoin_prices))
```

    59295.5
    4102.3

#### Agregaciones en _N_-dimensiones

```python
ndimensional = np.array([
    [
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9],
    ],
    [
        [1, 2, 3],
        [1, 2, 3],
        [1, 2, 3],
    ],
])

ndimensional.shape
```

    (2, 3, 3)

```python
np.mean(ndimensional)
```

    3.5

```python
axis_0 = np.mean(ndimensional, axis=0)

# [[ (1 + 1) / 2, (2 + 2) / 2, (3 + 3) / 2 ]
#  [ (4 + 1) / 2, (5 + 2) / 2, (6 + 3) / 2 ]
#  [ (7 + 1) / 2, (8 + 2) / 2, (9 + 3) / 2 ]]
print(axis_0.shape)
print(axis_0)
```

    (3, 3)
    [[1.  2.  3. ]
     [2.5 3.5 4.5]
     [4.  5.  6. ]]

```python
axis_1 = np.mean(ndimensional, axis=1)

# [[ (1 + 4 + 7) / 3, (2 + 5 + 8) / 3, (3 + 6 + 9) / 3 ]
#  [ (1 + 1 + 1) / 3, (2 + 2 + 2) / 3, (3 + 3 + 4) / 3 ]]
print(axis_1.shape)
print(axis_1)
```

    (2, 3)
    [[4. 5. 6.]
     [1. 2. 3.]]

```python
axis_2 = np.mean(ndimensional, axis=2)

# [[ (1 + 2 + 3) / 3, (4 + 5 + 6) / 3, (7 + 8 + 9) / 3 ]
#  [ (1 + 2 + 3) / 3, (1 + 2 + 3) / 3, (1 + 2 + 3) / 3 ]]
print(axis_2.shape)
print(axis_2)
```

    (2, 3)
    [[2. 5. 8.]
     [2. 2. 2.]]

### Persistencia

En algún momento vamos a querer almacenar nuestros arreglos, _NumPy_ nos deja hacerlo en forma de archivos de texto (1 y 2 dimensiones):

```python
to_save = np.array([
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
])

np.savetxt("multidimensional.txt", to_save)

loaded = np.loadtxt("multidimensional.txt")

print(loaded)
```

    [[1. 2. 3.]
     [4. 5. 6.]
     [7. 8. 9.]]

### Extra - velocidades

```python
a_list = list(range(100_000))
b_list = list(range(100_000))

a_array = np.array(a_list)
b_array = np.array(b_list)

def add_list(a,b):
    for i in range(len(a)):
        a[i] += b[i]

def add_array(a,b):
    a += b
```

```python
%timeit add_list(a_list,b_list)
```

    12 ms ± 266 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)

```python
%timeit add_array(a_list,b_list)
```

    658 µs ± 162 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

---

## Referencias

<table style="margin:0; max-width: 1000px;">
    <tbody>
        <tr>
            <td>
                <a href="https://thatcsharpguy.com">
                    <img src="/assets/general/Sharp@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://twitter.com/io_exception">
                    <img src="/assets/general/Twitter@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://tcsg.dev/discord">
                    <img src="/assets/general/Discord@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://github.com/thatcsharpguy/df">
                    <img src="/assets/general/GitHub@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://youtube.com/thatcsharpguy">
                    <img src="/assets/general/YouTube@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://youtu.be/iLIQykiASs4">
                    <img src="/assets/general/EnVivo@1x.png" />
                </a>
            </td>
            <td>
                <a href="https://twitch.tv/thatcsharpguy">
                    <img src="/assets/general/Twitch@1x.png" />
                </a>
            </td>
        </tr>
    </tbody>
</table>

### Libros

- **Elegant SciPy: The Art of Scientific Python**: [México](https://amzn.to/3c0ZNZM) &middot; [España](https://amzn.to/30WWmge) · [US](https://amzn.to/2PbkbhC)
- **High Performance Python: Practical Performant Programming for Humans**: [México](https://amzn.to/310hEtt) &middot; [España](https://amzn.to/3cVZsXD) · [US](https://amzn.to/3s9eUFW)

### Sitios web

- **From Python to NumPy** - [https://www.labri.fr/perso/nrougier/from-python-to-numpy/](https://www.labri.fr/perso/nrougier/from-python-to-numpy/)
- **100 NumPy excercises** - [https://github.com/rougier/numpy-100](https://github.com/rougier/numpy-100)
- **NumPy Cheat Sheet — Python for Data Science** - [https://www.dataquest.io/blog/numpy-cheat-sheet/](https://www.dataquest.io/blog/numpy-cheat-sheet/)

```python

```
