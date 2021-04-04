---
layout: post
title: "02a: Más sobre NumPy"
date: 2021-03-21 14:00:00
summary: "Les quiero contar más sobre NumPy"
image: http://i3.ytimg.com/vi/0_QIYyMBZN0/maxresdefault.jpg
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
                <a href="https://youtu.be/0_QIYyMBZN0">
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

```python
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import numpy as np
```

### _Playground_

<img src="/assets/02/alfred-kenneally-UsgLeLorRuM-unsplash.jpg" width="600px" />
Photo by <a href="https://unsplash.com/@alken?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Alfred Kenneally</a> on <a href="/s/photos/eagle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

```python
eagle = mpimg.imread("assets/02/alfred-kenneally-UsgLeLorRuM-unsplash.jpg")
plt.imshow(eagle)
```

    <matplotlib.image.AxesImage at 0x113b77640>

![png](output_4_1.png)

```python
eagle.shape
```

    (1280, 1920, 3)

## Flip, rotate

```python
ej = np.array([[1, 2], [3, 4]])
print(ej)
np.flipud(ej)
```

    [[1 2]
     [3 4]]





    array([[3, 4],
           [1, 2]])

```python
upside_down = np.flipud(eagle)  # eagle[::-1,...].copy()
plt.imshow(upside_down)
```

    <matplotlib.image.AxesImage at 0x113c396d0>

![png](output_8_1.png)

```python
left_to_right = np.fliplr(eagle)  # eagle[:, ::-1].copy()
plt.imshow(left_to_right)
```

    <matplotlib.image.AxesImage at 0x113cb41c0>

![png](output_9_1.png)

```python
rotated = np.rot90(eagle)
plt.imshow(rotated)
```

    <matplotlib.image.AxesImage at 0x113d19820>

![png](output_10_1.png)

## Uniendo arreglos

<img src="/assets/02/stack-concatenate-valid-joining.png" width="50%" />

<img src="/assets/02/stack-concatenate-invalid-joining.png" width="50%" />

```python
v1 = np.full((3,), 1)
v2 = np.full((3,), 2)
v3 = np.full((3,), 3)
print(v1, v2, v3)
```

    [1 1 1] [2 2 2] [3 3 3]

```python
m1 = np.full((3, 3), 1)
m2 = np.full((3, 3), 2)
m3 = np.full((3, 3), 3)
print(m1)
print(m2)
print(m3)
```

    [[1 1 1]
     [1 1 1]
     [1 1 1]]
    [[2 2 2]
     [2 2 2]
     [2 2 2]]
    [[3 3 3]
     [3 3 3]
     [3 3 3]]

### Concatenación

```python
v = np.concatenate((v1, v2))
print(v.shape, v)
```

    (6,) [1 1 1 2 2 2]

```python
v = np.concatenate((v1, v2), axis=0)
print(v.shape, v)
```

    (6,) [1 1 1 2 2 2]

```python
v = np.concatenate((v1, v2, v3), axis=0)
print(v.shape, v)
```

    (9,) [1 1 1 2 2 2 3 3 3]

```python
v = np.concatenate((m1, m2), axis=1)
print(v.shape)
print(v)
```

    (3, 6)
    [[1 1 1 2 2 2]
     [1 1 1 2 2 2]
     [1 1 1 2 2 2]]

```python
v = np.concatenate((m1, m2), axis=0)
print(v.shape)
print(v)
```

    (6, 3)
    [[1 1 1]
     [1 1 1]
     [1 1 1]
     [2 2 2]
     [2 2 2]
     [2 2 2]]

### _Stacking_

```python
# np.stack
v = np.stack((v1, v2, v3))
print(v.shape)
print(v)
```

    (3, 3)
    [[1 1 1]
     [2 2 2]
     [3 3 3]]

```python
v = np.stack((v1, v2, v3), axis=1)
print(v.shape)
print(v)
```

    (3, 3)
    [[1 2 3]
     [1 2 3]
     [1 2 3]]

```python
v = np.hstack((v1, v2, v3))
print(v.shape)
print(v)
```

    (9,)
    [1 1 1 2 2 2 3 3 3]

```python
v = np.vstack((v1, v2, v3))
print(v.shape)
print(v)
```

    (3, 3)
    [[1 1 1]
     [2 2 2]
     [3 3 3]]

```python
v = np.dstack((v1, v2, v3))
print(v.shape)
print(v)
```

    (1, 3, 3)
    [[[1 2 3]
      [1 2 3]
      [1 2 3]]]

```python
v = np.dstack((v1, v2, v3))
print(v.shape)
print(v)
```

    (1, 3, 3)
    [[[1 2 3]
      [1 2 3]
      [1 2 3]]]

```python
v = np.stack((m1, m2, m3))
print(v.shape)
print(v)
```

    (3, 3, 3)
    [[[1 1 1]
      [1 1 1]
      [1 1 1]]

     [[2 2 2]
      [2 2 2]
      [2 2 2]]

     [[3 3 3]
      [3 3 3]
      [3 3 3]]]

```python
v = np.stack((m1, m2, m3), axis=1)
print(v.shape)
print(v)
```

    (3, 3, 3)
    [[[1 1 1]
      [2 2 2]
      [3 3 3]]

     [[1 1 1]
      [2 2 2]
      [3 3 3]]

     [[1 1 1]
      [2 2 2]
      [3 3 3]]]

### Observaciones sobre unir arreglos

- Concatenamos sobre dimensiones existentes
- Apilamos sobre nuevas dimensiones

## Combinación de transformaciones

```python
primer_canal = eagle[:, :, 0]
plt.imshow(primer_canal, cmap="gray")
```

    <matplotlib.image.AxesImage at 0x113d88e50>

![png](output_31_1.png)

```python
half_image = primer_canal[: len(primer_canal) // 4 * 3]
plt.imshow(half_image, cmap="gray")
print(half_image.shape)
```

    (960, 1920)

![png](output_32_1.png)

```python
new_image = np.dstack(
    [half_image, np.zeros_like(half_image), half_image]  # Rojo  # Verde  # Azul
)
plt.imshow(new_image)
```

    <matplotlib.image.AxesImage at 0x113e603d0>

![png](output_33_1.png)

```python
upside_down = np.flipud(new_image)
left_to_right = np.fliplr(upside_down)
two_eagle = np.concatenate((new_image, left_to_right))
plt.figure(figsize=(15, 15))
plt.imshow(two_eagle)
```

    <matplotlib.image.AxesImage at 0x113ecb2b0>

![png](output_34_1.png)

```python
ejemplo = np.array([[1, 2, 3, 4], [5, 6, 7, 8]])
print(ejemplo)
np.roll(ejemplo, (1, -2), axis=(0, 1))
```

    [[1 2 3 4]
     [5 6 7 8]]





    array([[7, 8, 5, 6],
           [3, 4, 1, 2]])

```python
plt.figure(figsize=(10, 10))
shift = 100
shifted_left = np.roll(new_image, shift, axis=(1))
shifted_right = np.roll(left_to_right, -shift, axis=(1))
two_eagle = np.concatenate((shifted_left, shifted_right))
plt.imshow(two_eagle)
```

    <matplotlib.image.AxesImage at 0x113f2ffd0>

![png](output_36_1.png)

```python
color_change_1 = np.roll(shifted_left, 2, axis=(2))
color_change_2 = np.roll(shifted_right, 1, axis=(2))
color_change_2 = np.where(color_change_2 < 30, 0, color_change_2)
two_eagle = np.concatenate((color_change_1, color_change_2))
plt.figure(figsize=(10, 10))
plt.imshow(two_eagle)
```

    <matplotlib.image.AxesImage at 0x113fa0a00>

![png](output_37_1.png)

## `np.where`

```python
array = np.ones((5, 4)) * np.array([1, 2, 3, 4])
array_neg = np.ones((5, 4)) * np.array([1, 2, 3, 4]) * -9
print(array)
print(array_neg)
```

    [[1. 2. 3. 4.]
     [1. 2. 3. 4.]
     [1. 2. 3. 4.]
     [1. 2. 3. 4.]
     [1. 2. 3. 4.]]
    [[ -9. -18. -27. -36.]
     [ -9. -18. -27. -36.]
     [ -9. -18. -27. -36.]
     [ -9. -18. -27. -36.]
     [ -9. -18. -27. -36.]]

```python
np.where(array % 2 == 0, 100, -800)
```

    array([[-800,  100, -800,  100],
           [-800,  100, -800,  100],
           [-800,  100, -800,  100],
           [-800,  100, -800,  100],
           [-800,  100, -800,  100]])

## `np.nonzero`

```python
array = np.array([[1, 0, 0], [0, 2, 0], [3, 4, 0]])
array
```

    array([[1, 0, 0],
           [0, 2, 0],
           [3, 4, 0]])

```python
columnas, filas = np.nonzero(array)

for x, y in zip(columnas, filas):
    print(f"({x}, {y})", array[x, y])
```

    (0, 0) 1
    (1, 1) 2
    (2, 0) 3
    (2, 1) 4

## Ordenación

### _Sort_

Crea una **copia ordenada** del arreglo que recibe como parámetro. Quicksort es el algoritmo usado por default.

```python
arreglo = np.random.randint(0, 30, size=(6, 6))
print(arreglo)
```

    [[22 12  5 26  0  1]
     [ 5  6 22 19 20  9]
     [24  5 17 20 19  0]
     [28  8  9 17 16 12]
     [ 8 24 18 23 18  0]
     [23 21 18  4 25 14]]

```python
np.sort(arreglo[0])
```

    array([ 0,  1,  5, 12, 22, 26])

```python
copia = np.array(arreglo[0])
print(copia)
copia.sort()  # In Place
print(copia)
```

    [22 12  5 26  0  1]
    [ 0  1  5 12 22 26]

```python
np.sort(arreglo, axis=1)
```

    array([[ 0,  1,  5, 12, 22, 26],
           [ 5,  6,  9, 19, 20, 22],
           [ 0,  5, 17, 19, 20, 24],
           [ 8,  9, 12, 16, 17, 28],
           [ 0,  8, 18, 18, 23, 24],
           [ 4, 14, 18, 21, 23, 25]])

### `np.argsort`

```python
arreglo[0]
```

    array([22, 12,  5, 26,  0,  1])

```python
indices = np.argsort(arreglo[0])
print(indices)
```

    [4 5 2 1 0 3]

```python
[4, 4, 5, 23, 26, 26]
```

    [4, 4, 5, 23, 26, 26]

```python
print(arreglo[0][indices])
```

    [ 0  1  5 12 22 26]

## `np.argmax` y `np.argmin`

```python
arreglo[0]
```

    array([22, 12,  5, 26,  0,  1])

```python
maximo = np.argmax(arreglo[0])
minimo = np.argmin(arreglo[0])
print(maximo, minimo)
print(arreglo[0][maximo], arreglo[0][minimo])
```

    3 4
    26 0

```python
arreglo
```

    array([[22, 12,  5, 26,  0,  1],
           [ 5,  6, 22, 19, 20,  9],
           [24,  5, 17, 20, 19,  0],
           [28,  8,  9, 17, 16, 12],
           [ 8, 24, 18, 23, 18,  0],
           [23, 21, 18,  4, 25, 14]])

```python
np.argmin(arreglo, axis=1)
```

    array([4, 0, 5, 1, 5, 3])

## Vectorización

Ya hablamos de la vectorización... pero, ¿cómo es que podemos aplicar esto en _"la realidad_"?

### "Regla de oro"

#### 🚫 `for` 🚫

El objetivo de la vectorización es, entre otras cosas, evitar el programar ciclos explícitamente.

- Analiza las variables de entrada
- Genera arreglos auxiliares
- Aplica las operaciones
- Recolecta los resultados

### Alternativas al `if`

```python
sample = np.random.uniform(-20, 20, (15,))
print(sample)
```

    [  4.56730224  19.02880331 -11.96054461  -0.5768552    3.88897091
      -4.9145975  -17.05731846 -10.8866641    0.60906842  -3.03007902
       2.58552579  19.40135034   9.00661466 -16.5622198  -12.71291223]

#### `np.where`

```python
copy = sample.copy()

threshold = 2

for i in range(len(copy)):
    if copy[i] < threshold:
        copy[i] *= 10
print(copy)
```

    [   4.56730224   19.02880331 -119.60544609   -5.76855196    3.88897091
      -49.14597504 -170.57318457 -108.86664097    6.0906842   -30.30079021
        2.58552579   19.40135034    9.00661466 -165.62219797 -127.12912226]

```python
copy = sample.copy()
print(np.where(copy < threshold, copy * 10, copy))
```

    [   4.56730224   19.02880331 -119.60544609   -5.76855196    3.88897091
      -49.14597504 -170.57318457 -108.86664097    6.0906842   -30.30079021
        2.58552579   19.40135034    9.00661466 -165.62219797 -127.12912226]

#### `np.maximum`

```python
copy = sample.copy()
```

```python
print(np.maximum(copy, 0))
```

    [ 4.56730224 19.02880331  0.          0.          3.88897091  0.
      0.          0.          0.60906842  0.          2.58552579 19.40135034
      9.00661466  0.          0.        ]

#### `np.clip`

```python
print(np.clip(copy, -6, 6))
```

    [ 4.56730224  6.         -6.         -0.5768552   3.88897091 -4.9145975
     -6.         -6.          0.60906842 -3.03007902  2.58552579  6.
      6.         -6.         -6.        ]

#### `np.isnan`

```python
aa = np.array([np.nan, 10, 15, np.nan, 16])
promedio = aa[~np.isnan(aa)].mean()
np.where(np.isnan(aa), promedio, aa)
```

    array([13.66666667, 10.        , 15.        , 13.66666667, 16.        ])

```python
print(~np.isnan(aa))
print(np.isnan(aa))
```

    [False  True  True False  True]
    [ True False False  True False]

## Ecuaciones

### Sumatorias (y acumulacionse en general)

Imagina una ecuación así

$$\sum_{i=0}^{} \cos(x_i^{2})$$

```python
x = np.random.uniform(0, 1, 10)

np.sum(np.cos(x ** 2))
```

    8.992406423625107

Supongamos que el índice del elemento $i$ aparece dentro del cuerpo de la sumatoria:

$$\sum_{i=0}^{} \cos(x_i) * i$$

La solución es generar un arreglo con el índice $i$ de antemano:

```python
i = np.arange(len(x))
print(i)
np.sum(np.cos(x) * i)
```

    [0 1 2 3 4 5 6 7 8 9]





    37.43336056246062

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
                <a href="https://youtu.be/0_QIYyMBZN0">
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
- **Chapter 3 Numerical calculations with NumPy** - [http://kestrel.nmt.edu/~raymond/software/python_notes/paper003.html](http://kestrel.nmt.edu/~raymond/software/python_notes/paper003.html)
