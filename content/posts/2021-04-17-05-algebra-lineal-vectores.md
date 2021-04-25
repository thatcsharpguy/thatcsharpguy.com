---
layout: post
title: "05: Vectores - Álgebra Lineal"
date: 2021-04-17 14:00:00
summary: "Vamos a ver un poco de álgebra lineal aplicada a vectores"
image: http://i3.ytimg.com/vi/bgEML8Re2ks/maxresdefault.jpg
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
                <a href="https://youtu.be/bgEML8Re2ks">
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

- `numpy`
- `matplotlib`

```python
import numpy as np
import matplotlib.pyplot as plt
from df.display_algebra import show_vector, show_normed_vects, draw_covariance_ellipse
```

## Espacios vectoriales

Supongamos que tenemos un conjunto de vectores $[x_1, x_2, \dots x_d], x_i \in \mathbb{R}$ (vamos a hablar sobre números reales, pero podemos generalizar a cualquier otro conjunto de números) de una dimensión $d$, nosotros podríamos decir que este conjunto de vectores existe dentro de un espacio vectorial si se cumplen ciertas condiciones...

Digamos que tenemos los vectores:

$$\vec{x} = [x_1, x_2, \dots, x_d]$$  
$$\vec{y} = [y_1, y_2, \dots, y_d]$$

### Operaciones en espacios vectoriales

Entre las condiciones que deben cumplirse están que debemos tener bien definidas dos operaciones:

- **multiplicación por un escalar**: de tal modo que $\alpha \vec{x}$ está definido para cualquier número escalar $\alpha$. En nuestro caso de los reales, $\alpha \vec{x} = [\alpha x_1, \alpha x_2, \dots, \alpha x_d]$

- **suma de vectores**: de tal modo que $\vec{x} + \vec{y}$, En nuestro caso de los reales, $\vec{x} + \vec{y} = [x_1 + y_1, x_2 + y_2, \dots x_d + y_d]$

<small>Existen 10 axiomas basadas en estas dos operaciones que deben cumplirse.</small>

Existe un subconjunto de espacios vectoriales, llamado **espacio vectorial topológico** bajo los cuales debemos definir otro par de operaciones:

- **norma**: $||\vec{x}||$ que define la longitud de un vector, útil para medir y comparar vectores

- **producto interno**: $\langle \vec{x} | \vec{y} \rangle$ or $\vec{x} \bullet \vec{y}$ permite comparar los ángulos de dos vectores. El producto interno de dos vectores ortogonales es 0. Para vectores de números reales $\vec{x} \bullet \vec{y} = x_1 y_1 + x_2 y_2 + x_3 y_3 \dots x_d y_d$.

Las operaciones que definimos son importantes para la ciencia de datos porque nos permite hablar de que un par de vectores están cerca el uno del otro, nos permite sumarlos, compararlos y escalarlos.

<img src="/assets/05/vectores.png" />

### Recordando _NumPy_...

```python
x = np.array([0, 1, 1])
y = np.array([4, 5, 6])
a = 2


def pp(text, value):
    print(text.rjust(10), "=", str(value))


pp("a", a)
pp("x", x)
pp("y", y)
pp("a * x", a * x)
pp("x + y", x + y)
pp("||x||", np.linalg.norm(x))
pp("x · y", np.dot(x, y))
```

             a = 2
             x = [0 1 1]
             y = [4 5 6]
         a * x = [0 2 2]
         x + y = [4 6 7]
         ||x|| = 1.4142135623730951
         x · y = 11

- **`np.linalg.norm`**
- **`np.dot`**

### Información como vectores

Las representaciones vectoriales son de vital importancia en el aprendizaje automático, puesto que la información es más fácilmente procesada por un algoritmo cuando es representada en forma de vectores.

Pero antes de hablar de vectores, es importante entender que muchas veces la información no está en forma vectorial por naturaleza, en muchos casos el proceso es el siguiente:

<img src="/assets/05/experiment.png" />

### Un clásico de machine learning...

<img src="/assets/05/flower.png" />

<small>Imagen original en el curso de Data Fundamentals <a href="https://www.gla.ac.uk/undergraduate/degrees/computingscience/?card=course&code=COMPSCI4073">https://www.gla.ac.uk/undergraduate/degrees/computingscience/?card=course&code=COMPSCI4073</a></small>

## Operaciones vectoriales

### Sumas y multiplicaciones

Ya hablamos de las sumas y multiplicaciones _elemento-elemento_; una consecuencia de esto es que podemos introducir el concepto de sumas ponderadas:

$$\lambda_1 x_1 + \lambda_2 x_2 + \dots + \lambda_n x_n$$

```python
a = np.array([1.0, 1.0])
b = np.array([0.0, 0.5])
c = np.array([0.5, 0.0])

origin = np.array([0, 0])


fig = plt.figure(figsize=(15, 5), dpi=200)
ax = fig.gca()


show_vector(ax, origin, a, "$\\vec{a}$")
show_vector(ax, origin, b, "$\\vec{b}$")
show_vector(ax, origin, c, "$\\vec{c}$")

show_vector(ax, a, a + b, "$\\vec{a} + \\vec{b}$")
show_vector(ax, a + b, a + b + c, "$\\vec{a} + \\vec{b} + \\vec{c}$")
show_vector(ax, a + b + c, a + b + c - b, "$\\vec{a} + \\vec{b} + \\vec{c} - \\vec{b}$")


ax.set_xlim(-1.5, 2.0)
ax.set_ylim(-0.5, 2.0)
ax.set_aspect(1.0)
ax.legend(loc="center left")
```

    <matplotlib.legend.Legend at 0x11a223f40>

![png](output_10_1.png)

### Tamaño de un vector

Una de las formas para calcular la "magnitud" o la "longitud" de un vector es la siguiente fórmula:

$$ \|\vec{x}\|\_2 = \sqrt{x_0^2 + x_1^2 + x_2^2 + \dots + x_n^2 } $$

Esta fórmula es conocida como la _norma_ de un vector; _NumPy_ nos la ofrece a través de la función `np.linalg.norm`:

```python
x = np.array([1.0, 10.0, -5.0])
y = np.array([1.0, -4.0, 8.0])
print(np.linalg.norm(x))
print(np.linalg.norm(y))
```

    11.224972160321824
    9.0

Esta norma es conocida como la **norma euclidiana**, y que también nos ayuda a calcular la distancia entre dos vectores $\vec{a}$ y $\vec{b}$:

$$||\vec{x}-\vec{y}||_2$$

#### Diferentes tamaños

Como ya lo mencioné, existen diversas maneras de determinar la magnitud de un vector:

| Notación             | Nombre           | Efecto                        |
| -------------------- | ---------------- | ----------------------------- |
| $\|x\|$ or $\|x\|_2$ | Norma Euclidiana | Distancia "ordinaria"         |
| $\|x\|_1$            | Norma Manhattan  | Suma de los valores absolutos |
| $\|x\|_0$            | Pseudo-Norma 0   | Número de valores $\neq 0$    |
| $\|x\|_\inf$         | Norma máxima     | Elemento mayor                |
| $\|x\|_{-\inf}$      | Norma mínima     | Elemento menor                |

```python
test_vector = [2, 0, 5, -5, -1, 0]
```

```python
np.linalg.norm(test_vector, 0)  # Pseudo-norma cero
```

    4.0

```python
np.linalg.norm(test_vector, 1)  # Manhattan
```

    13.0

```python
np.linalg.norm(test_vector, np.inf)  # Máximo absoluto
```

    5.0

```python
np.linalg.norm(test_vector, -np.inf)  # Mínimo absoluto
```

    0.0

```python
np.linalg.norm(test_vector, 2)
```

    7.416198487095663

```python
np.linalg.norm(test_vector)
```

    7.416198487095663

#### Vectores unitarios

Un vector cuya norma sea $1$ es conocido como vector unitario (esto implica que diferente normas nos llevan a diferentes vectores unitarios).

Cuando nosotros tomamos un vector y lo dividimos entre su norma podemos decir que el vector ha sido normalizado, por ejemplo con la norma euclidiana:

$$\vec{x}_n = \vec{x} \frac{1}{\|\vec{x}\|_2}$$

```python
def print_v(x):
    return print(np.around(x, 2))
```

```python
x = np.random.uniform(1, 5, 4)
norm = np.linalg.norm(x)
x_norm = x / norm

print_v(x)
print(norm)
print_v(x_norm)
```

    [3.13 3.24 4.45 2.12]
    6.6797898562340565
    [0.47 0.49 0.67 0.32]

#### Representación gráfica

```python
x = np.random.normal(0, 1, (50, 2))
```

```python
fig = plt.figure(figsize=(15, 5), dpi=200)
ax = fig.gca()

show_normed_vects(ax, x, 2)
```

![png](output_27_0.png)

### Producto interno

Existe otra operación que nos da la noción del ángulo que existe entre dos vectores, esta operación se conoce como **producto interno**:

$$\cos \theta = \frac{\vec{x} \bullet \vec{y}} {||\vec{x}|| \  ||\vec{y}||}$$

La operación en el numerador es la suma de la multiplicación _elemento-a-elemento_:

$$\vec{x} \bullet \vec{y} = \sum_i{x_i y_i}$$

Si estamos hablando de vectores unitarios podemos ignorar el denominador (la norma de ambos vectores es $1$).

Evidentemente, el producto interno está solamente definido para vectores con dimensiones iguales.

```python
x = [1, 2, 3, 4]
y = [4, 3, 2, 1]

print(np.inner(x, y))
```

    20

El producto interno es una generalización del producto punto.

Este producto nos ayuda cuando queremos comparar un par de vectores, para revisar si es que ambos apuntan a la misma dirección.

### Producto externo

Existe otra clase de producto que dados dos vectores $\vec{x}$ y $\vec{y}$ está definido de la siguiente manera:

$$
\vec{x} \otimes \vec{y} = \begin{bmatrix}
x_1 y_1 & x_1 y_2 & \dots & x_1 y_m \\
x_2 y_1 & x_2 y_2 & \dots & x_2 y_m \\
\dots\\
x_n y_1 & x_n y_2 & \dots & x_n y_m \\
\end{bmatrix}
$$

Sí, es una matriz cuyas entradas están definidas por $A=\vec{x}\otimes\vec{y}$ are: $A_{ij} = x_i  y_j$ .

### Producto cruz

En tres dimensiones existe un producto llamado **producto cruz**, dados dos vectores $\vec{a}$ e $\vec{b}$, el resultado es un tercero que está separado exactamente 90 grados de los dos vectores que se usaron para generarlo.

$${\displaystyle {\begin{alignedat}{3}\mathbf {\vec{a}} &=a_{1}\mathbf {\color {blue}{i}} &&+a_{2}\mathbf {\color {red}{j}} &&+a_{3}\mathbf {\color {lime}{k}}\end{alignedat}}}$$

$${\displaystyle {\begin{alignedat}{3}\mathbf {\vec{b}} &=b_{1}\mathbf {\color {blue}{i}} &&+b_{2}\mathbf {\color {red}{j}} &&+b_{3}\mathbf {\color {lime}{k}} \end{alignedat}}}$$

$${\displaystyle {\begin{aligned}\mathbf {\vec{a} \times \vec{b}} &=(a_{2}b_{3}\mathbf {i} +a_{3}b_{1}\mathbf {j} +a_{1}b_{2}\mathbf {k} )-(a_{3}b_{2}\mathbf {i} +a_{1}b_{3}\mathbf {j} +a_{2}b_{1}\mathbf {k} )\\&=(a_{2}b_{3}-a_{3}b_{2})\mathbf {i} +(a_{3}b_{1}-a_{1}b_{3})\mathbf {j} +(a_{1}b_{2}-a_{2}b_{1})\mathbf {k}\end{aligned}}}$$

## Estadísticas sobre vectores

Ya hablamos sobre algunas operaciones en vectores; podemos utilizarlas para definir operaciones estadísticas (que existen en el mundo de los números reales).

### Centroide geométrico

Supongamos que tenemos una colección de $N$ vectores, podemos definir el promedio de esta colección como la suma de todos los vectores dividida entre $N$:

$$mean(\vec{x_1}, \vec{x_2}, \dots, \vec{x_n}) = \frac{1}{N} \sum_i {\vec{x_i}}$$  
En el mundo de los vectores este vector promedio se conoce como el **centroide** del conjunto de $N$ vectores definidos anteriormente.

_NumPy_ nos hace muy sencillo calcular el vector promedio usando `np.mean(vectors, axis=0)`:

```python
centro = 25
spread = 5
vectors = np.random.normal(centro, spread, (50, 2))


fig = plt.figure(figsize=(10, 10), dpi=200)
ax = fig.gca()

centroid = np.mean(vectors, axis=0)

ax.scatter(vectors[:, 0], vectors[:, 1], label="Vectores")
ax.scatter([centroid[0]], [centroid[1]], s=50, label="Centroid")
ax.legend()
# ax.set_xlim((centro-spread, centro+spread))
# ax.set_ylim((centro-spread, centro+spread))
```

    <matplotlib.legend.Legend at 0x11a4ef4c0>

![png](output_34_1.png)

### Centrando un dataset

Tomando el centroide, podemos realizar una operación muy importante conocida como **centrar un dataset** si tomamos todos los vectores de nuestro conjunto y le restamos este centroide. Esto nos va a llevar a que nuestro nuevo conjunto de vectores (llamémosle "centrado") tiene un **promedio cero**:

```python
fig = plt.figure(figsize=(10, 10), dpi=200)
ax = fig.gca()
ax.axhline(0, c="k", ls=":")
ax.axvline(0, c="k", ls=":")

centroid = np.mean(vectors, axis=0)
centrado = vectors - centroid
new_centroid = np.mean(centrado, axis=0)


ax.scatter(vectors[:, 0], vectors[:, 1], label="Vectores originales")
ax.scatter([centroid[0]], [centroid[1]], s=50, label="Centroide original")

ax.arrow(
    centroid[0],
    centroid[1],
    new_centroid[0] - centroid[0],
    new_centroid[1] - centroid[1],
    head_width=1,
    linestyle="-",
    width=0.2,
    overhang=1,
    length_includes_head=True,
    color="black",
)

ax.scatter(centrado[:, 0], centrado[:, 1], label="Vectores centrados")
ax.scatter([new_centroid[0]], [new_centroid[1]], s=50, label="Nuevo centroide")
ax.grid()
ax.legend()
```

    <matplotlib.legend.Legend at 0x11a5a9130>

![png](output_36_1.png)

### Midiendo la extensión (_Measuring the spread_)

Generalizando el concepto de varianza (que nos ayuda a medir la extensión de un conjunto de datos) podemos encontrar la extensión de nuestro conjunto de datos en el caso multidimensional. Recordando la forma unidimensional:

$$\sigma^2 =  \frac{1}{N-1} \sum_{i=0}^{N-1}{(x_i - \mu)^2}$$

(La varianza es el promedio de las sumas del cuadrado de las diferencias de cada elemento con el promedio...)

De la varianza podemos obtener la desviación estándar si calculamos la raíz cuadrada de $\sigma$.

En el caso multidimensional, tenemos que calcular algo conocido como **covarianza**, esta covarianza nos dice qué tan ...

Este es el promedio de las diferencias de cada elemento de una columna con el promedio del resto de las columnas:

$$\Sigma_{ij} = \frac{1}{N-1}  \sum_{k=1}^{N} (X_{ki}-\mu_i)(X_{kj}-\mu_j)$$

En _NumPy_ podemos calcular la covarianza con `np.cov`:

```python
x = np.random.normal(2, 1, (100, 2)) @ np.array(
    [[0.5, 0.1], [-0.9, -3.0]]
)  # 30 filas y 4 columnas

sigma_np = np.cov(x, rowvar=False)
sigma_np
```

    array([[1.05824031, 2.79711505],
           [2.79711505, 9.01629243]])

#### Elipses de covarianza

Podemos ver gráficamente la covarianza a través de elipses; este elipse parece "capturar" todo nuestro dataset. El centroide del dataset es el centro del elipse y la matriz de covarianza representa la forma del mismo:

```python
fig = plt.figure(figsize=(5, 5), dpi=200)
ax = fig.gca()
ax.scatter(x[:, 0], x[:, 1], s=5)
draw_covariance_ellipse(ax, x, 0.5)
draw_covariance_ellipse(ax, x, 1.0)
draw_covariance_ellipse(ax, x, 2.0)
```

![png](output_40_0.png)

## Espacios vectoriales de grandes dimensiones

Trabajar con información representada en forma de arreglos de una, dos y hasta tres dimensiones es sencillo: estamos familiarizados con ella, podemos graficarla, es fácil visualizarla mentalmente... sin embargo, muchas veces, la ciencia de datos involucra espacios vectoriales de grandes dimensiones.

Piensa en una imagen pequeña, de unos 500 pixeles de ancho por 500 de alto, esta información es representada en un vector de $500 \times 500 \times 3 = 750,000$ entradas.

### La maldición de la dimensionalidad 😱

¿Cómo se escucha trabajar con vectores de 1000, 5000, 10000 elementos?

Hay algoritmos que se desempeñan perfectamente cuando operamos con vectores en espacios de pocas dimensiones pero cuando aumentamos la cantidad de elementos en los vectores comienzan a fallar.

La **maldición de la dimensionalidad** hace que buscar en un espacio sea complejo afectando a los algoritmos que funcionan particionando el dataset: Árboles de decisión, Redes Neuronales, Máquinas de Vectores, Nearest Neighbours, K-means...

Entre más dimensiones, es más difícil generalizar.

## Matrices

Ya habíamos hablado sobre matrices, ahora vamos a ver algunas de las operaciones que podemos hacer con algo conocido como álgebra de matrices, también conocida como álgebra lineal:

### Suma de matrices

$$
\begin{equation} A + B = \begin{bmatrix}
a_{1,1} + b_{1,1} & a_{1,2} + b_{1,2} & \dots & a_{1,m} + b_{1,m} \\
a_{2,1} + b_{2,1} & a_{2,2} + b_{2,2} & \dots & a_{2,m} + b_{2,m} \\
\dots & \dots & \dots & \dots  \\
a_{n,1} + b_{n,1} & a_{n,2} + b_{n,2} & \dots & a_{n,m} + b_{n,m} \\
\end{bmatrix}
\end{equation}
$$

### Multiplicación por escalar

$$
\begin{equation} cA = \begin{bmatrix}
ca_{1,1}  & ca_{1,2} & \dots & ca_{1,m}  \\
ca_{2,1} & ca_{2,2}  & \dots & ca_{2,m} \\
\dots & \dots & \dots & \dots  \\
ca_{n,1}  & ca_{n,2} & \dots & ca_{n,m} \\
\end{bmatrix}
\end{equation}
$$

### Multiplicación entre matrices

El resultado de una multiplicación de matrices $A$ y $B$ es otra matriz:

$$AB = C$$

La multiplicación de matrices solamente está definida para matrices $A$ y $B$ si:

- $A$ tiene dimensiones $p \times q$
- $B$ tiene dimensiones $q \times r$

Puedes ver la multiplicación de matrices definida con la siguiente función:

$$\begin{equation}C_{ij}=\sum_k a_{ik} b_{kj} \end{equation}$$

La multiplicación de matrices viene integrada en _NumPy_:

- `np.dot(A, B)`
- `A @ B`

```python
A = np.random.randint(0, 10, (3, 5))
B = np.random.randint(0, 10, (5, 2))

dot_result = np.dot(A, B)
at_result = A @ B

print(A.shape, B.shape)
print()
print(dot_result)
print()
print(at_result)
print()
print(np.allclose(dot_result, at_result))
```

    (3, 5) (5, 2)

    [[ 46  36]
     [ 69  62]
     [122  84]]

    [[ 46  36]
     [ 69  62]
     [122  84]]

    True

### Matrices y vectores

También podemos multiplicar matrices por un vector... de nuevo, las reglas anteriormente definidas aplican:

$$
\vec{x} =
\begin{bmatrix}
x_1\\
x_2\\
\dots\\
x_n\\
\end{bmatrix}
$$

El vector de acá arriba se conoce como **vector columna** de dimensión $D \times 1$, mientras que el de abajo se conoce como **vector fila** de dimensión $1 \times D$:

$$
\vec{y} =
\begin{bmatrix}
x_1 &
x_2 &
\dots &
x_n
\end{bmatrix}
$$

Dada nuestra definición de multiplicación, multiplicar $\vec{x}\vec{y}$ resultará en una matriz de $D \times D$ (el producto externo 😉), mientras que multiplicar $\vec{y}\vec{x}$ resultará en un escalar (el producto interno 😉):

```python
x = np.array([[1, 2, 3]])
y = np.array([[4, 5, 6]])

print("x", x.shape)
print(x, "\n")
print("y", y.shape)
print(y, "\n")
```

    x (1, 3)
    [[1 2 3]]

    y (1, 3)
    [[4 5 6]]

```python
print(np.outer(x, y))
print(x.T @ y)
```

    [[ 4  5  6]
     [ 8 10 12]
     [12 15 18]]
    [[ 4  5  6]
     [ 8 10 12]
     [12 15 18]]

```python
print(np.inner(y, x))
print(y @ x.T)
```

    [[32]]
    [[32]]

```python
# Multiplicación matricial

x = np.array([1, 2, 3])
A = np.array([[1, 0, 1], [1, 0, 0], [0, 1, 1]])  # 3x3
print(x.shape)
print(x)
print(A.shape)
print(A)
print()
print(A @ x)
```

    (3,)
    [1 2 3]
    (3, 3)
    [[1 0 1]
     [1 0 0]
     [0 1 1]]

    [4 1 5]

```python
x = np.array([1, 2, 3])


print(np.dot(x, x))
print(x @ x.T)
```

    14
    14

> ⚠️ A diferencia de con los números reales, la multiplicación de matrices no es conmutativa:

$$AB \neq BA$$

> ⚠️ Aún que las dimensiones sean compatibles, en general al multiplicación de matrices no es conmutativa.

### Exponenciación de matrices

Una vez que tenemos a nuestra disposición la multiplicación, podemos definir el hecho de multiplicar una matriz **cuadrada** por si misma como la exponenciación matricial:

$$A^4=AAAA$$

## Matrices especiales

### Matriz diagonal

Matrices para las que los elementos $A_{ij} == 0$ en donde $i \neq j$ se llaman matrices diagonales, tienen significado especial en el álgebra lineal.

Podemos convertir un vector de longitud $D$ en una matriz de $D \times D$ con los elementos del vector en la diagonal usando `np.diag`:

```python
x = np.random.randint(0, 10, (4,))
diag_x = np.diag(x)

print(x)
print()
print(diag_x)
```

    [7 7 7 9]

    [[7 0 0 0]
     [0 7 0 0]
     [0 0 7 0]
     [0 0 0 9]]

Es posible extraer el vector de la diagonal de una matriz usando... `np.diag`:

```python
x = np.random.randint(0, 10, (4, 4))
diag_x = np.diag(x)

print(x)
print()
print(diag_x)
```

    [[1 8 8 2]
     [6 6 5 4]
     [9 5 2 0]
     [3 1 1 8]]

    [1 6 2 8]

### Matriz identidad

Una matriz cuadrada cuyos elementos son $0$ en todas partes excepto en la diagonal en donde los valores son $1$ es conocida como una matriz identidad.

La matriz identidad no provoca ningún cambio cuando la usamos en una multiplicación. En _NumPy_ podemos usar `np.eye(n)` para generar una matriz identidad de tamaño `n`:

```python
x = np.random.randint(0, 10, (3, 3))
I = np.eye(3, dtype=int)

print(x)
print(x @ I)
print(I @ x)
# print(I * x) # Not the same!
```

    [[9 0 0]
     [0 4 8]
     [5 9 4]]
    [[9 0 0]
     [0 4 8]
     [5 9 4]]
    [[9 0 0]
     [0 4 8]
     [5 9 4]]

### Matriz cero

Ah... lo que dice el título

### Matriz cuadrada

🔲🔳◾️▫️

### Matriz triangular

Una matriz es considerada triangular si los elementos debajo o arriba de la diagonal son cero.

Existen dos subtipos:

- **Triangular superior**

$$
\begin{bmatrix}
1 & 2 & 3 & 4 \\
0 & 5 & 6 & 7 \\
0 & 0 & 8 & 9 \\
0 & 0 & 0 & 10 \\
\end{bmatrix}
$$

- **Triangular inferior**

$$
\begin{bmatrix}
1 & 0 & 0 & 0 \\
2 & 3 & 0 & 0 \\
4 & 5 & 6 & 0 \\
7 & 8 & 9 & 10 \\
\end{bmatrix}
$$

Podemos usar `np.tri(n)` para generar una matriz triangular inferior de $1$ usando _NumPy_:

```python
triangular = np.tri(4)

print(triangular)
print()
print(triangular.T)
```

    [[1. 0. 0. 0.]
     [1. 1. 0. 0.]
     [1. 1. 1. 0.]
     [1. 1. 1. 1.]]

    [[1. 1. 1. 1.]
     [0. 1. 1. 1.]
     [0. 0. 1. 1.]
     [0. 0. 0. 1.]]

### Matrices dispersas

A pesar de que no hemos hablado de estas matrices (ni hablaremos en el futuro en este curso) sobre matrices dispersas, son una parte central en la ciencia de datos.

Imagina que en Spotify tienen una matriz en donde las filas son las todas las canciones y las columnas los usuarios, y los valores dentro de la matriz representan si al usuario le gusta o no dicha canción, ¿cómo crees que se vería esa matriz?

|                    | Feregrino | Alma      | Benito    | ... | Terry     | Destiny   |
| ------------------ | --------- | --------- | --------- | --- | --------- | --------- |
| La Puerta Negra    | ${\bf 1}$ | $0$       | $0$       |     | $0$       | $0$       |
| Flux               | $0$       | ${\bf 1}$ | $0$       |     | ${\bf 1}$ | $0$       |
| La mesa del rincón | ${\bf 1}$ | $0$       | $0$       |     | $0$       | $0$       |
| Andar conmigo      | ${\bf 1}$ | ${\bf 1}$ | $0$       |     | ${\bf 1}$ | $0$       |
| ...                |           |           |           | ... |           |           |
| Dark Horse         | $0$       | ${\bf 1}$ | ${\bf 1}$ |     | $0$       | ${\bf 1}$ |

Lo más seguro es que la gran mayoría de las personas no disfruten de la gran mayoría de las canciones, entonces la matriz de acá arriba muy probablemente estará dominada por $0$, con muy pocos $1$ esparcidos aquí y allá.

¿Cómo lidiarías con ella?

---

## Resources

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
                <a href="https://youtu.be/bgEML8Re2ks">
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

### Sitios web

- **Espacios y subespacios vectoriales** - [https://aga.frba.utn.edu.ar/espacios-y-subespacios-vectoriales/](https://aga.frba.utn.edu.ar/espacios-y-subespacios-vectoriales/)

- **High-Dimensional Space** - [https://www.cs.cmu.edu/~venkatg/teaching/CStheory-infoage/chap1-high-dim-space.pdf](https://www.cs.cmu.edu/~venkatg/teaching/CStheory-infoage/chap1-high-dim-space.pdf)

- **Maldición de la dimensión** - [https://es.wikipedia.org/wiki/Maldici%C3%B3n_de_la_dimensi%C3%B3n](https://es.wikipedia.org/wiki/Maldici%C3%B3n_de_la_dimensi%C3%B3n)

- **List of named matrices** - [https://en.wikipedia.org/wiki/List_of_named_matrices](https://en.wikipedia.org/wiki/List_of_named_matrices)

### Videos

- **3blue1brown Linear Algebra series** -[https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)

### Libros

- **Introduction to applied linear algebra** - [http://stanford.edu/~boyd/vmls/vmls.pdf](http://stanford.edu/~boyd/vmls/vmls.pdf)

- **Coding the matrix** - [https://codingthematrix.com/](https://codingthematrix.com/)
