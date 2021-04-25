---
layout: post
title: "06: Matrices - Álgebra Lineal"
date: 2021-04-24 14:00:00
summary: "Vamos a ver un poco de álgebra lineal aplicada a matrices"
image: http://i3.ytimg.com/vi/Vdn-8j8yFSk/maxresdefault.jpg
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
                <a href="https://youtu.be/Vdn-8j8yFSk">
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
- `pandas`
- `matplotlib`
- `scikit-learn`

```python
import numpy as np
import pandas as pd
import random
import matplotlib.pyplot as plt
from df.display_algebra import draw_covariance_ellipse
```

## Descomposición matricial

¿Recuerdas los factores primos? recuerdas que un número puede ser obtenido a partir de sus factores primos, por ejemplo:

$$42 = 2 \times 3 \times 7$$

Este proceso se conoce como descomposición matemática. Como el título de esta sección lo indica, las matrices también se pueden descomponer de esta manera en objetos relativamente más simples.

Estas descomposiciones pueden ser usadas para diversos fines:

- Análisis de datos representados como matrices
- Ejecución eficiente de operaciones matriciales

En ejemplos más tangibles: podemos usar descomposiciones para [sistemas de recomendación](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf), [motores de búsqueda](https://www.maa.org/press/periodicals/loci/joma/the-linear-algebra-behind-search-engines-an-advanced-vector-space-model-lsi), [compresión de imágenes](http://timbaumann.info/svd-image-compression-demo/), [reconocimiento facial](http://openimaj.org/tutorial/eigenfaces.html), y [otras aplicaciones](https://heartbeat.fritz.ai/applications-of-matrix-decompositions-for-machine-learning-f1986d03571a).

### Puntos fijos

Supongamos que tenemos un punto $x$, tal que si le aplicamos una función $f$, el resultado es $x$. Es decir si:

$$f(x) = x$$

Podemos decir que $x$ es un punto fijo de $f$. Por ejemplo, supongamos que $f(x) = x^2$:

- Cuando $x = 0$, $f(x) = 0$
- Cuando $x = 1$, $f(1) = 1$

Tanto $x = 0$ como $x = 1$ son considerados puntos fijos de $x^2$.

> 🤔 existen dos tipos de puntos fijos: estables y no estables. Se dice que un punto fijo es estable si cualquier valor en su vecindad hace que la función se acerque al punto fijo, mientras que se dice que uno no es estable si cualquier valor en su vecindad hace que la función se aleje de este.

Podemos obtener los puntos fijos de una función de manera _ingenua_ eligiendo un valor aleatorio $x$ y aplicando sucesivamente la regla $x_t=f(x_{t-1})$ hasta llegar a un punto estable, ese es tu punto fijo:

```python
def find_fixed_point(f, r, eps=1e-5):

    fr = f(r)
    history = [(r, fr)]
    # ¿Ya dejamos de movernos?
    while np.abs(fr - r) > eps and np.abs(fr) < np.inf:
        r = fr
        fr = f(r)
        history.append((r, fr))

    return fr, np.array(history)
```

```python
def function(x):
    return np.cos(x)


r = np.random.uniform(-5, 5)  # random starting point
fixed, history = find_fixed_point(function, r)

print(f"Punto fijo {fixed:0.5}")
print(f"f({fixed:0.5}) = {function(fixed):0.5}")
```

    Punto fijo 0.73908
    f(0.73908) = 0.73909

## Eigen.. ¿qué?

De cierto modo, cada matriz representan una función conocida como [aplicación lineal](https://es.wikipedia.org/wiki/Aplicaci%C3%B3n_lineal), dentro de las aplicaciones lineales existen las [transformaciones lineales] que transforman elementos entre espacios vectoriales.

### Eigenvalores y eigenvectores

Si bien las transformaciones lineales tienen puntos fijos, existen valores análogos, y aún más interesantes para analizar de una matriz:

- **Eigenvectores**
- **Eigenvalores**

> **eigen** = propio (o característico).

#### Método de las potencias

Vamos a tomar una matriz cuadrada $A$ y aplicarla a un vector aleatorio $\vec{x}$, al resultado volverle a aplicar $A$ y luego volverle a aplicar $A$... es decir, calculamos:

$$AAA \dots AA\vec{x}$$
$$A^n\vec{x}$$

En la práctica esto causaría que el resultado crezca hasta infinito o colapse a cero. Podemos normalizar el resultado hacer algo para contrarrestar este efecto:

$$\begin{equation}x_n = \frac{Ax_{n-1}}{\|Ax_{n-1}\|_\infty}\end{equation}$$

El resultado a cada paso será forzado a ser un vector unitario usando al norma mínima $L_\infty$. Finalmente, a este método lo vamos a conocer como el **método de las potencias** o _power iteration method_.

```python
def power_iterate(A, x, n):
    for i in range(n):
        x = A @ x  # multiply
        x = x / np.linalg.norm(x, np.inf)  # normalize

    return x / np.linalg.norm(x, 2)
```

Comencemos con nuestra matriz $A$:

```python
A = np.random.normal(0, 1, (2, 2))
print(A)
```

    [[ 0.71243771  1.2818496 ]
     [ 1.32566962 -0.61522188]]

Seguimos con un vector cualquiera:

```python
cualquiera = np.random.normal(0, 3, (2,))
print(cualquiera)
```

    [0.99530246 0.09380106]

```python
# El vector resultante -casi siempre- es el mismo
eigenvector = power_iterate(A, cualquiera, n=500)
print(eigenvector)
```

    [0.84862822 0.52898974]

Este vector es conocido como el **eigenvector principal**, y es una característica de la matriz.

La matriz $A$ toma nuestro vector y la única transformación que le hace es escalarlo (hacerlo más grande) sin rotaciones ni sesgos.

Para calcular el factor de escalamiento podemos...

```python
ratio = (A @ eigenvector) / eigenvector
eigenvalue = ratio[0]  # Todos los elementos tienen el mismo valor.
print(eigenvalue)
```

    1.5114746452729027

El escalar que ves más arriba es conocido como el **eigenvalor principal**, llamémoslo $\lambda$ por el momento, y satisface esta ecuación:

$$A\vec{x}_i = \lambda_i\vec{x}_i$$

```python
uno = A @ eigenvector
dos = eigenvector * eigenvalue

np.allclose(uno, dos)
```

    True

Cada vector $\vec{x}_i$ que satisfaga la ecuación es un **eigenvector** y cada $\lambda_i$ que cumpla esta ecuación es un **eigenvalue**. Estos elementos vienen en pares.

### Otros eigenvalores

El método de las potencias nos ayuda a encontrar **un solo** vector, sin embargo pueden existir múltiples pares de **eigen cosas**, para encontrar los otros, podemos seguir este algoritmo:

#### Encontrar más valores...

Mientras tanto en NumPy...

```python
evals, evecs = np.linalg.eig(A)
print(evals[0])
print(evecs[:, 0])
```

    1.5114746452729062
    [0.84862822 0.52898974]

## El espectro... _eigenspectro_.

A la secuencia eigenvalores ordenados por su valor absoluto se le conoce como el eigenespectro de una matriz; esto nos puede ayudar a encontrar versiones simplificadas de nuestras matrices.

Supongamos que tenemos un dataset de 18K jugadores de fútbol:

```python
fifa19 = pd.read_csv("assets/06/fifa19.csv")
# https://fifauteam.com/fifa-19-attributes-guide/

attributes = [
    "Acceleration",
    "SprintSpeed",  # Pace
    "Finishing",
    "LongShots",
    "Penalties",
    "Positioning",
    "ShotPower",
    "Volleys",  # Shooting
    "Crossing",
    "Curve",
    "FKAccuracy",
    "LongPassing",
    "ShortPassing",
    "Vision",  # Passing
    "Agility",
    "Balance",
    "BallControl",
    "Composure",
    "Dribbling",
    "Reactions",  # Dribbling
    "HeadingAccuracy",
    "Interceptions",
    "Marking",
    "SlidingTackle",
    "StandingTackle",  # Defending
    "Aggression",
    "Jumping",
    "Stamina",
    "Strength",  # Physical
    # 'GKDiving', 'GKHandling', 'GKKicking', 'GKPositioning', 'GKReflexes', # Goalkeeping
]

player_stats = fifa19[attributes].copy().fillna(axis="index", method="backfill")
player_stats.head()
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Acceleration</th>
      <th>SprintSpeed</th>
      <th>Finishing</th>
      <th>LongShots</th>
      <th>Penalties</th>
      <th>Positioning</th>
      <th>ShotPower</th>
      <th>Volleys</th>
      <th>Crossing</th>
      <th>Curve</th>
      <th>...</th>
      <th>Reactions</th>
      <th>HeadingAccuracy</th>
      <th>Interceptions</th>
      <th>Marking</th>
      <th>SlidingTackle</th>
      <th>StandingTackle</th>
      <th>Aggression</th>
      <th>Jumping</th>
      <th>Stamina</th>
      <th>Strength</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>91.0</td>
      <td>86.0</td>
      <td>95.0</td>
      <td>94.0</td>
      <td>75.0</td>
      <td>94.0</td>
      <td>85.0</td>
      <td>86.0</td>
      <td>84.0</td>
      <td>93.0</td>
      <td>...</td>
      <td>95.0</td>
      <td>70.0</td>
      <td>22.0</td>
      <td>33.0</td>
      <td>26.0</td>
      <td>28.0</td>
      <td>48.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>59.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>89.0</td>
      <td>91.0</td>
      <td>94.0</td>
      <td>93.0</td>
      <td>85.0</td>
      <td>95.0</td>
      <td>95.0</td>
      <td>87.0</td>
      <td>84.0</td>
      <td>81.0</td>
      <td>...</td>
      <td>96.0</td>
      <td>89.0</td>
      <td>29.0</td>
      <td>28.0</td>
      <td>23.0</td>
      <td>31.0</td>
      <td>63.0</td>
      <td>95.0</td>
      <td>88.0</td>
      <td>79.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>94.0</td>
      <td>90.0</td>
      <td>87.0</td>
      <td>82.0</td>
      <td>81.0</td>
      <td>89.0</td>
      <td>80.0</td>
      <td>84.0</td>
      <td>79.0</td>
      <td>88.0</td>
      <td>...</td>
      <td>94.0</td>
      <td>62.0</td>
      <td>36.0</td>
      <td>27.0</td>
      <td>33.0</td>
      <td>24.0</td>
      <td>56.0</td>
      <td>61.0</td>
      <td>81.0</td>
      <td>49.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>57.0</td>
      <td>58.0</td>
      <td>13.0</td>
      <td>12.0</td>
      <td>40.0</td>
      <td>12.0</td>
      <td>31.0</td>
      <td>13.0</td>
      <td>17.0</td>
      <td>21.0</td>
      <td>...</td>
      <td>90.0</td>
      <td>21.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>13.0</td>
      <td>21.0</td>
      <td>38.0</td>
      <td>67.0</td>
      <td>43.0</td>
      <td>64.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>78.0</td>
      <td>76.0</td>
      <td>82.0</td>
      <td>91.0</td>
      <td>79.0</td>
      <td>87.0</td>
      <td>91.0</td>
      <td>82.0</td>
      <td>93.0</td>
      <td>85.0</td>
      <td>...</td>
      <td>91.0</td>
      <td>55.0</td>
      <td>61.0</td>
      <td>68.0</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>76.0</td>
      <td>63.0</td>
      <td>90.0</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 29 columns</p>
</div>

Calculando la matriz de covarianza y vemos los eigenvectores y los valores correspondientes a cada uno de ellos:

```python
players_cov = np.cov(player_stats.values, rowvar=False)
eigs, eigv = np.linalg.eig(players_cov)

order = np.argsort(eigs)
eigs, eigv = eigs[order], eigv[order]

fig = plt.figure()
ax = fig.gca()
ax.bar(np.arange(len(eigs)), list(reversed(eigs)))
ax.set_xlabel("Eigenvalor")
ax.set_ylabel("Amplitud")
ax.set_title("Eigenspectrum of the FIFA 19 dataset")
```

    Text(0.5, 1.0, 'Eigenspectrum of the FIFA 19 dataset')

![png](output_26_1.png)

Los eigenvectores nos ayudan a identificar los ejes a lo largo de los que nuestro dataset varía:

```python
def print_vector(vector):
    properties = []
    for attribute, value in zip(attributes, vector):
        properties.append(f"{attribute:<20s} {value:+.03}")
    results = "\n".join(properties)
    print(results)


print("Eigenvector 1")
print_vector(eigv[0])
print()
print("Eigenvector 2")
print_vector(eigv[0])
```

    Eigenvector 1
    Acceleration         -0.202
    SprintSpeed          -0.0314
    Finishing            -0.0103
    LongShots            +0.0974
    Penalties            -0.0264
    Positioning          -0.171
    ShotPower            -0.223
    Volleys              -0.104
    Crossing             +0.0122
    Curve                -0.139
    FKAccuracy           -0.239
    LongPassing          +0.182
    ShortPassing         +0.49
    Vision               +0.249
    Agility              +0.12
    Balance              -0.6
    BallControl          -0.000102
    Composure            -0.0518
    Dribbling            -0.00559
    Reactions            -0.038
    HeadingAccuracy      -0.055
    Interceptions        +0.0145
    Marking              -0.104
    SlidingTackle        -0.0457
    StandingTackle       +0.0985
    Aggression           +0.0528
    Jumping              -0.156
    Stamina              -0.0677
    Strength             +0.0961

    Eigenvector 2
    Acceleration         -0.202
    SprintSpeed          -0.0314
    Finishing            -0.0103
    LongShots            +0.0974
    Penalties            -0.0264
    Positioning          -0.171
    ShotPower            -0.223
    Volleys              -0.104
    Crossing             +0.0122
    Curve                -0.139
    FKAccuracy           -0.239
    LongPassing          +0.182
    ShortPassing         +0.49
    Vision               +0.249
    Agility              +0.12
    Balance              -0.6
    BallControl          -0.000102
    Composure            -0.0518
    Dribbling            -0.00559
    Reactions            -0.038
    HeadingAccuracy      -0.055
    Interceptions        +0.0145
    Marking              -0.104
    SlidingTackle        -0.0457
    StandingTackle       +0.0985
    Aggression           +0.0528
    Jumping              -0.156
    Stamina              -0.0677
    Strength             +0.0961

Estos dos vectores son las direcciones con mayor varianza en nuestro dataset... difícil de ver e imaginar porque estamos hablando de un espacio de 29 dimensiones.

## PCA

Lo que acabamos de hacer acá arriba se conoce como **análisis de componentes principales** o _principal component análisis_ (_PCA_).

Los componentes principales de un dataset son los eigenvectores de su matriz de covarianza. Usando este análisis de componentes principales podemos encontrar una proyección de bajas dimensiones para nuestros datos si proyectamos nuestro dataset en los vectores principales:

```python
def pca(data, n_components):
    cov = np.cov(data, rowvar=False)
    eigs, eigv = np.linalg.eig(cov)

    order = np.argsort(eigs)
    eigs, eigv = eigs[order], eigv[order]
    eig_order = np.argsort(np.abs(eigs))
    components = []
    for i in range(n_components):
        components.append(data @ eigv[eig_order[-i - 1]])
    return np.stack(components).T
```

```python
components = pca(player_stats.values, 2)
```

```python
player_ids = [158023, 20801, 190871, 238789, 231747, 194765, 208722, 209331] + [
    156519,
    186302,
    192041,
    187478,
    167524,
    213445,
    217167,
    224103,
    173434,
    186551,
    187208,
    199569,
    190485,
    233260,
    187705,
    186979,
    167532,
    212377,
    183743,
    139229,
    203283,
    171377,
    222645,
    192015,
    #    186513, 232316, 239545, 187722, 237498, 169415, 202786, 192046,
    #    235368, 235123, 236434, 237978, 246188, 243157, 236842, 240177,
    #    237657, 244349, 244611, 193164, 242823, 210426, 244743, 233535,
    244615,
    212475,
    246294,
    244831,
    240107,
    239548,
    244637,
]


index = fifa19[fifa19["ID"].isin(set(player_ids))].index
comp = components[index]
```

```python
len(comp), len(index), len(set(player_ids)), len(player_ids)
```

    (39, 39, 39, 39)

```python
fifa19[fifa19["Name"].str.contains("Mané")]
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }

</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>ID</th>
      <th>Name</th>
      <th>Age</th>
      <th>Photo</th>
      <th>Nationality</th>
      <th>Flag</th>
      <th>Overall</th>
      <th>Potential</th>
      <th>Club</th>
      <th>...</th>
      <th>Composure</th>
      <th>Marking</th>
      <th>StandingTackle</th>
      <th>SlidingTackle</th>
      <th>GKDiving</th>
      <th>GKHandling</th>
      <th>GKKicking</th>
      <th>GKPositioning</th>
      <th>GKReflexes</th>
      <th>Release Clause</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>58</th>
      <td>58</td>
      <td>208722</td>
      <td>S. Mané</td>
      <td>26</td>
      <td>https://cdn.sofifa.org/players/4/19/208722.png</td>
      <td>Senegal</td>
      <td>https://cdn.sofifa.org/flags/136.png</td>
      <td>86</td>
      <td>87</td>
      <td>Liverpool</td>
      <td>...</td>
      <td>80.0</td>
      <td>42.0</td>
      <td>42.0</td>
      <td>38.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>15.0</td>
      <td>7.0</td>
      <td>14.0</td>
      <td>€102.7M</td>
    </tr>
    <tr>
      <th>1920</th>
      <td>1920</td>
      <td>216749</td>
      <td>Carlos Mané</td>
      <td>24</td>
      <td>https://cdn.sofifa.org/players/4/19/216749.png</td>
      <td>Portugal</td>
      <td>https://cdn.sofifa.org/flags/38.png</td>
      <td>75</td>
      <td>81</td>
      <td>Sporting CP</td>
      <td>...</td>
      <td>73.0</td>
      <td>38.0</td>
      <td>34.0</td>
      <td>33.0</td>
      <td>16.0</td>
      <td>14.0</td>
      <td>10.0</td>
      <td>9.0</td>
      <td>11.0</td>
      <td>€19.8M</td>
    </tr>
  </tbody>
</table>
<p>2 rows × 89 columns</p>
</div>

```python
fig = plt.figure(figsize=(10, 5), dpi=100)
ax = fig.gca()

ax.scatter(comp[:, 0], comp[:, 1])
for i, player_id in enumerate(player_ids):
    ax.text(
        comp[i, 0],
        comp[i, 1],
        fifa19[fifa19["ID"] == player_id]["Name"].values[0],
        fontsize=6,
    )

ax.set_xlabel("Componente 1")
ax.set_ylabel("Componente 2")
ax.set_title("Componentes principales FIFA19")
```

    Text(0.5, 1.0, 'Componentes principales FIFA19')

![png](output_36_1.png)

### Proyecciones de pocas dimensiones

Las proyecciones de pocas dimensiones son herramientas de vital importancia en la ciencia de datos exploratoria. PCA es una forma de lograr esta tarea, sin embargo, no es la única. Existe otro algoritmo llamado _t-SNE_ que, al igual que PCA nos permite descomponer nuestra información para proyectar nuestro dataset:

```python
from sklearn.manifold import TSNE

t_reduce = TSNE()
xy_2d = t_reduce.fit_transform(player_stats.values)[index]
```

```python
fig = plt.figure(figsize=(10, 5), dpi=100)
ax = fig.add_subplot(1, 1, 1)

ax.scatter(xy_2d[:, 0], xy_2d[:, 1])
for i, player_id in enumerate(player_ids):
    ax.text(
        xy_2d[i, 0],
        xy_2d[i, 1],
        fifa19[fifa19["ID"] == player_id]["Name"].values[0],
        fontsize=6,
    )


ax.set_xlabel("t-SNE componente 1")
ax.set_ylabel("t-SNE componente 2")
ax.set_title("t-SNE transformation of the whisky dataset")
```

    Text(0.5, 1.0, 't-SNE transformation of the whisky dataset')

![png](output_39_1.png)

## Otras propiedades de las matrices

### Traza

La traza $\text{tr}$ de una matriz cuadrada es la suma de los elementos en la diagonal:

$$\text{tr}(A) = a_{1,1} + a_{2,2} + \dots + a_{n,n}$$

Esto es igual que la suma de los eigenvalores:

$$\text{tr}(A) = \sum_{i=1}^n \lambda_i$$

### Determinante

El determinante $\text{det}$ de una matriz es igual a la multiplicación de los eigenvalores:

$$\text{det}(A) = \prod_{i=1}^n \lambda_i$$

Cuando alguno de los eigenvalores de la matriz es $0$ el determinante es $0$, lo cual tiene implicaciones para la matriz.

> ⚠️ Hay matrices con eigenvalores complejos, sin embargo... no las veremos aquí.

## Inversion de matrices

Recordarás las operaciones básicas sobre las matrices:

- Multiplicación por un escalar: $cA$
- Suma matricial: $A + B$
- Multiplicación matricial: $AB$
- Transposición: $A^T$

Ves la suma y la multiplicación y tal vez te preguntes... ¿hay división?

La operación más parecida es la inversa de una matriz, que nos permite:

- $A^{-1}(A\vec{x}) = \vec{x}$
- $A^{-1}A = I$
- $(A^{-1})^{-1} = A$

Sin embargo esta operación, (la inversa) no está siempre definida para todas las matrices. En particular, la inversa no está definida para matrices **no cuadradas** y con **determinante = $0$**.

Una matriz es llamada **singular** si su determinante es 0 (y por tanto no invertible) y es llamada **no singular** si es posible invertirla.

#### Algoritmo

Hay diversas maneras de calcular la inversa de una matriz, incluyendo un algoritmo recursivo; este algoritmo recursivo funciona, pero solo para matrices pequeñas. Más adelante hablaremos de una forma efectiva para invertir matrices.

### ¿Qué problemas resuelve la inversión de matrices?

#### Sistemas lineales

Imagina que tenemos la siguiente matriz:

$$
A = \begin{bmatrix}
0.5 & 1.0 & 2.0\\
1.0 & 0.5 & 0.0\\
0.6 & 1.1 & -0.3\\
\end{bmatrix}
$$

Esta matriz representa una aplicación lineal que opera sobre vectores tridimensionales $\vec{x}$, y que produce vectores 3D $\vec{y}$. Cada uno de las entradas de este vector es una suma ponderada de las entradas del vector $\vec{x}$:

$$
y_1 = 0.5x_1 + 1.0x_2 +2.0x_3\\
y_2 = 1.0x_1 +0.5x_2 +0.0x_3\\
y_3 = 0.6x_1 + 1.1x_2 - 0.3x_3
$$

```python
A = np.array([[0.5, 1.0, 2.0], [1.0, 0.5, 0.0], [0.6, 1.1, -0.3]])
x = np.array([1, -1, 1])

print(A @ x)
```

    [ 1.5  0.5 -0.8]

Viéndolo desde otra perspectiva, esta es la forma en la que sistemas de ecuaciones simultaneas pueden ser representadas de la siguiente forma:

$$A\vec{x} = \vec{y}$$

Esto es conocido como un sistema de ecuaciones lineares.

Partiendo de esto, y de que ahora sabemos que la inversa de una matriz existe, podríamos pensar que la solución es bastante trivial:

$$
A^{-1}A\vec{x}=A^{-1}\vec{y} \\
I \vec{x} = A^{-1} \vec{y} \\
\vec{x} = A^{-1}\vec{y}
$$

> Recuerda que la inversa de una matriz solamente está definida si la matriz es cuadrada

En la práctica, los sistemas lineales casi nunca son resueltos invirtiendo directamente la matriz; hay problemas de estabilidad numérica así como de complejidad algorítmica que lo hacen un procedimiento inviable.

### Soluciones aproximadas

En lugar de tratar de invertir de una vez la matriz, resuelven el problema de forma iterativa, una de las formas es utilizando optimización, algo que veremos en el futuro.

## Descomposición en valores singulares

La eigendescomposición que vimos hace poco solamente funciona para matrices cuadradas, sin embargo no siempre nuestros problemas van a venir en esta forma; es aquí en donde entra otra forma de descomponer matrices:

La _singular value decomposition_ (SVD) es una forma general de descomponer cualquier matriz $A$. Es una de las grandes herramientas del álgebra lineal.

_SVD_ descompone una matriz en tres partes:

$$A = U \Sigma V$$

En donde:

- $A$ es la matriz inicial de $m \times n$,
- $U$ es una matriz **ortonormal** de $m \times m$, conocida como **vectores singulares izquierdos**,
- $V$ es una matriz **ortonormal** de $n \times n$, conocida como **vectores singulares derechos**,
- $\Sigma$ es una matriz diagonal de $m \times n$, la diagonal son los **valores singulares**

> Una matriz ortonormal $U$ es una matriz que cumple que $U^{-1} =U^T$ y que las columnas y filas tienen norma unitaria.

La diagonal de $\Sigma$ son los **valores singulares** que son parecidos a los _eigenvectores_ sin embargo no son lo mismo. Por ejemplo, los valores singulares siempre son números positivos.

En _NumPy_...

```python
A = np.array([[3, 2, 3, 2], [2, 9, 4, 2], [4, 1, 9, 8]])


u, sigma, v = np.linalg.svd(A)
true_sigma = np.zeros_like(A, dtype=np.float64)
np.fill_diagonal(true_sigma, sigma)
A_reconstructed = u @ true_sigma @ v


print(u)
print()
# print(sigma)
print(true_sigma)
print()
print(v)

print()
print(A_reconstructed)
```

    [[-0.32057561  0.02380932 -0.94692365]
     [-0.52903899  0.82473129  0.19984007]
     [-0.78571561 -0.56502338  0.25179268]]

    [[15.22220181  0.          0.          0.        ]
     [ 0.          7.6735174   0.          0.        ]
     [ 0.          0.          1.54974283  0.        ]]

    [[-0.33915378 -0.4065258  -0.66674476 -0.52455973]
     [-0.07026804  0.89987114 -0.22347735 -0.36790245]
     [-0.92526324  0.10098838  0.14500694  0.335652  ]
     [ 0.15467725  0.12153212 -0.69604762  0.69052343]]

    [[3. 2. 3. 2.]
     [2. 9. 4. 2.]
     [4. 1. 9. 8.]]

### SVD 🤝 eigendescomposición

La _SVD_ es:

- Obtener los eigenvectores de $A^T A$ para obtener $U$
- Obtener los eigenvectores de $A A^T$ para obtener $V$
- Calcular la raíz cuadrada de los eigenvalores de $A^T A$

### Aplicaciones de las descomposiciones

Una vez que tenemos la descomposición hay operaciones que se vuelven triviales:

$$A = U\Sigma V$$
$$A^{-1} = V^T \Sigma^\dagger U^T$$

- $\Sigma^\dagger$ es $\text{diag}(1.0/\Sigma_{ii})^T$

### Pseudo-inversa...

$\dots$

## Los valores singulares

Como te imaginarás, los valores singulares capturan algunos aspectos esenciales de una matriz.

- Se dice que una matriz es de **rango completo** si su número de valores singulares distintos de cero es igual al número de elementos en la diagonal de la matriz; de otro modo se trata de una matriz de **rango deficiente**.

- El **número condicional** de una matriz es la relación entre su valor singular más grande y el más pequeño; este número nos dice qué tan sensible es la aplicación lineal a cambios, es decir en la operación $A\vec{x} = \vec{y}$ nos dice si $\vec{y}$ cambia mucho o poco cuando realizamos pequeños cambios a $\vec{x}$. Se relaciona con los conceptos de matrices **bien condicionadas** y **mal condicionadas**.

### Los valores singulares y la singularidad

Habíamos dicho que una matriz es singular si es _no invertible_ y tiene $\text{det}(A) = 0$, esta definición es binaria, los conceptos de rango y número condicional nos ayudan a convertir esta distinción en un gradiente, usando estos dos números podemos responder la pregunta "¿Qué tan singular es la matriz?"

## Aplicaciones de las descomposiciones

### Operaciones _avanzadas_.

Ahora que podemos descomponer una matriz, podemos plantearnos el realizar operaciones como:

- Elevar una matriz a una potencia fraccionaria $A^{\frac{1}{3}}$
- Invertir una matriz ${A^{-1}}$
- Calcular el logaritmo de una matriz $\ln{A}$

La forma de hacerlo es "sencilla": ignoramos $U$ y $V$ y se le aplica la operación en cuestión a todos los elementos de $\Sigma$.

### Esferización (_sphering_)

El **esferización** (mas comúnmente conocido como blanqueamiento) consiste en remover todas las correlaciones lineales dentro de un dataset, es una forma de normalizar un conjunto de datos que se realiza antes de realizar un análisis.

Dado una matriz $X$:

$$X^* =(X-\mu)\Sigma^{-\frac{1}{2}}$$

en donde $\vec{\mu}$ es el **vector promedio** y $\Sigma$ es la **matriz de covarianza**.

El resultado de aplicar esta operación **centra el dataset** y modifica el dataset para que la matriz de covarianza sea **unitaria**.

```python
def apply_sphering(x):
    center_x = x - np.mean(x, axis=0)
    u, sigma, v = np.linalg.svd(np.cov(center_x, rowvar=False))

    # La magia
    sigma_inv_sqr = v.T @ np.diag(1.0 / np.sqrt(sigma)) @ u.T
    shphere_x = center_x @ sigma_inv_sqr

    return shphere_x
```

```python
X = np.random.normal(0, 1, (200, 2)) @ np.array([[0.1, 0.5], [-0.9, 1.0]]) + np.array(
    [2, 3]
)
X_sphered = apply_sphering(X)

fig = plt.figure(figsize=(10, 10))
ax = fig.gca()

ax.scatter(X[:, 0], X[:, 1], c="#99d8c9", label="Original")
ax.scatter(X_sphered[:, 0], X_sphered[:, 1], c="#feb24c", label="Esferada")

for dataset in [X, X_sphered]:
    for std in [0.5, 1, 2]:
        draw_covariance_ellipse(ax, dataset, std)

for one, two in zip(X, X_sphered):
    ax.plot([one[0], two[0]], [one[1], two[1]], alpha=0.2)

# ax.set_xlim(-6, 6)
# ax.set_ylim(-6, 6)

ax.axhline(0)
ax.axvline(0)
ax.set_aspect(1.0)
ax.legend()
ax.set_title("Esferización de un dataset")
```

    Text(0.5, 1.0, 'Esferización de un dataset')

![png](output_50_1.png)

### Aproximaciones de bajo rango

¿Recuerdan las matrices dispersas de la sesión pasada?

|                    | Feregrino | Alma      | Benito    | ... | Terry     | Destiny   |
| ------------------ | --------- | --------- | --------- | --- | --------- | --------- |
| La Puerta Negra    | ${\bf 1}$ | $0$       | $0$       |     | $0$       | $0$       |
| Flux               | $0$       | ${\bf 1}$ | $0$       |     | ${\bf 1}$ | $0$       |
| La mesa del rincón | ${\bf 1}$ | $0$       | $0$       |     | $0$       | $0$       |
| Andar conmigo      | ${\bf 1}$ | ${\bf 1}$ | $0$       |     | ${\bf 1}$ | $0$       |
| ...                |           |           |           | ... |           |           |
| Dark Horse         | $0$       | ${\bf 1}$ | ${\bf 1}$ |     | $0$       | ${\bf 1}$ |

Datasets como este tipo son el pan de cada día de compañías como Spotify, Netflix y Amazon. Lo que siempre están tratando de hacer es encontrar nuevos objetos para recomendarles a sus usuarios.

Como vimos anteriormente, la gran mayoría de los usuarios han escuchado/visto/comprado una cantidad mínima de canciones/películas/productos.

Centrándonos en el ejemplo de las canciones, una forma simplista de verlos podríamos encapsular a los usuarios dentro de grupos como "el fan de los tigres del norte", "el que solo escucha canciones de my chemical romance", "el fan de música de tienda de ropa"... sin embargo la realidad no funciona así, un modelo más realista es uno que representa a los usuarios como una suma ponderada:

$$\text{usuario} = 0.2 \times \text{tigres del norte} + 0.7 \times \text{mcr} + 0.1 \times \text{música de zara}$$

Esto nos permite usar solamente una parcialidad de la información de los usuarios para realizar recomendaciones; usamos la _SVD_ para realizar esto.

Podemos encontrar una aproximación de bajo rango truncando la _SVD_ y manteniendo solamente una fracción, digamos $K$, de los valores singulares, y las primeras $K$ columnas y filas de $U$ y $V$ respectivamente.

Esta es una forma de reducción dimensional.

[Ver recursos al final]

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
                <a href="https://youtu.be/Vdn-8j8yFSk">
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

- **The Linear Algebra Behind Search Engines** - [https://www.maa.org/press/periodicals/loci/joma/the-linear-algebra-behind-search-engines-introduction](https://www.maa.org/press/periodicals/loci/joma/the-linear-algebra-behind-search-engines-introduction)

- **Eigenfaces: Recovering Humans from Ghosts** - [https://towardsdatascience.com/eigenfaces-recovering-humans-from-ghosts-17606c328184](https://towardsdatascience.com/eigenfaces-recovering-humans-from-ghosts-17606c328184)

- **Eigenfaces for recognition** - [https://www.face-rec.org/algorithms/pca/jcn.pdf](https://www.face-rec.org/algorithms/pca/jcn.pdf)

- **Image Compression with Singular Value Decomposition** - [http://timbaumann.info/svd-image-compression-demo/](http://timbaumann.info/svd-image-compression-demo/)

- **Eigenvectors and eigenvalues** - [http://setosa.io/ev/eigenvectors-and-eigenvalues/](http://setosa.io/ev/eigenvectors-and-eigenvalues/)

- **Power Iteration | ML Wiki** - [http://mlwiki.org/index.php/Power_Iteration](http://mlwiki.org/index.php/Power_Iteration)

- **PCA Whitening** - [http://ufldl.stanford.edu/tutorial/unsupervised/PCAWhitening](http://ufldl.stanford.edu/tutorial/unsupervised/PCAWhitening)

- **Matrix Factorization for Movie Recommendations in Python** - [https://beckernick.github.io/\_posts/2016-11-10-matrix-factorization-recommender/](https://beckernick.github.io/_posts/2016-11-10-matrix-factorization-recommender/)
