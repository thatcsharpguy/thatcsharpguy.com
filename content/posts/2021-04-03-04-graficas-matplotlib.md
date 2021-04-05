---
layout: post
title: "04: Haciendo buenas gráficas"
date: 2021-04-03 14:00:00
summary: "Vamos a ver cuáles son las partes de una buena gráfica; y ver algunas herramientas como Matplotlib y Seaborn"
image: http://i3.ytimg.com/vi/7vZY6JdswKs/maxresdefault.jpg
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
                <a href="https://youtu.be/7vZY6JdswKs">
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
- `seaborn`
- `pandas`

```python
import numpy as np
import pandas as pd
```

## ¿Qué es la visualización de datos?

Es la representación gráfica de información y datos para consumo humano. Se usa para transformar conceptos abstractos como números, vectores, matrices... en gráficas de las cuales una persona puede observar patrones, tendencias, anomalías... y en generar extraer información sobre el elemento que se está intentando interpretar.

Algunos de los objetivos de la visualización son:

- Presentar relaciones y estructuras en los datos
- Resumir grandes cantidades de datos
- Resolver preguntas específicas

### Algunas gráficas

<img src="/assets/04/chart-types.png" style="max-width: 1000px" />

<small>↖[Top 2500 feelings](http://www.wefeelfine.org/press/pdfs-high-res/236-237.pdf), ↙ [Junk Charts](https://junkcharts.typepad.com/junk_charts/2011/04/worst-statistical-graphic-nominated.html), ↗[William Playfair](https://www.historyofinformation.com/detail.php?entryid=2929), ↘[A Tour Through the Visualization Zoo](https://homes.cs.washington.edu/~jheer//files/zoo/)</small>

- **Poco informativas**: Poco claras, engañosas, innecesarias
- **Informativas**: Concisas, simples, correctass
- **Atractivas**: Innovadoras, coloridas, estéticas
- **No Atractivas**: Sobrecargadas, aburridas, pobre estructura

> ⚠️ Nos vamos a enfocar en gráficas de información tabular bidimensionales

## _Layered Grammar of Graphics_

Las gráficas pueden ser expresadas en términos de un "lenguaje gráfico". Este lenguaje nos ayuda no solo especificando cómo convertir datos a imágenes, sino que también ayuda a los lectores a a interpretar y extraer información de las gráficas. Podemos extraer un ejemplo de este lenguaje del libro **Layered Grammar of Graphics** por Hadley Wickham.

### Partes de una gráfica

- **Stat** es información estadística obtenida de los datos; esta información es representda visualmente con la intención de resumir los datos. El promedio, y la desviación estandar o un histograma son ejemplos de **stats**.

- **Mapping** es una transformación de datos abstractos a un elemento visual; Toma un atributo de nuestros datos (puede ser inclusive un **stat**) y lo representa de forma visual usando una **scale** y **guide** para dotar de unidades a dicho atributo.

  - **Scale** especifica la transformación de los datos a valores visuales, por ejemplo, _Escala de Richter_ al tamaño de un circulo en pantalla, o _Altura de una persona_ a tamaño de una barra en la pantalla.

  - **Guide** especifica una referencia visual que le da sentido al **mapping**, nos dice cuál es la escala y el atributo que se está tratando de representar. Por ejemplo, las anotaciones en los ejes y las layendas que especifican qué significan los colores en una gráfica.

- **Geom** es la representación geométrica de la información después de haber pasado por el **mapping**. Ejemplos son las líneas y puntos visibles en cada gráfica.

- **Coord** es el sistema de coordenadas en el que se presenta la información; coordina cómo es que se muestran los **geoms** y las **guides** en nuestra gráfica.

- **Layer** agrupa un conjunto de **geoms**, cada **layer** contienen un **coord**. Podemos apilar múltiples **layers** en un mismo sistema de coordenadas.

- **Facet** agrupa un comjunto de **layers**, representa una "vista diferente" de un mismo conjunto de datos.

- **Figure** agrupa un conjunto de **facets**

- **Título** y **leyenda** cada figura estar acompañada de una explicación

<img src="/assets/04/grammar-of-graphics.png" />

## Anatomía de una figura

<img src="/assets/04/sphx_glr_anatomy_001.webp" style="max-width: 600px" />
<small>Anatomy of a figure <a href="https://matplotlib.org/stable/gallery/showcase/anatomy.html">https://matplotlib.org/stable/gallery/showcase/anatomy.html</a></small>

## Matplotlib y Seaborn

`matplotlib` es la biblioteca en la cual se centra todo el contenido de ahora en adelante. Pero matplotlib es solo la herramienta; los conceptos deben trascender independientemente de qué herramienta termines utilizando.

### Sobre Matplotlib

Es la piedra angular de múltiples paquetes de visualización existentes, es poderosa pero compleja; no se trata de decidir entre una y la otra muchas veces para lograr la visualización perfecta tenemos que usar algo como _seaborn_ para el 90% de la gráfica y _matplotlib_ para los detalles.

<img src="/assets/04/landscape-colors.png" style="max-width: 1000px" />
<small>Adaptation of Jake VanderPlas graphic about the Python visualization landscape, by Nicolas P. Rougier</small>

### ¿Mi recomendación?

Usa ambas, comienza las gráficas con `matplotlib`, créalas con la biblioteca de tu elección y terminalas con `matplotlib`.

> Matplotlib tiene dos interfaces: una basada en estado (herencia de MATLAB) y la orientada a objetos. Vamos a usar la API orientada a objetos. Como siempre, la clave es la consistencia, **elije una y úsala siempre**.

```python
import matplotlib.pyplot as plt
import seaborn as sns
```

```python
np.random.seed(42)
x = np.arange(100)
y1 = (0.1 * x) ** 2 + np.random.uniform(-20, 20, size=(100,))
y2 = (0.05 * x) ** 2 + np.random.uniform(-20, 20, size=(100,))
```

### API basada en estado

```python
plt.figure(figsize=(10, 5), dpi=75)
# figzize (ancho, alto) en pulgadas
# dpi - dots-per-inch
plt.scatter(x, y1, label="Clase 1")
plt.scatter(x, y2, label="Clase 2")
plt.title("Datos artificiales")
plt.xlabel("Eje X")
plt.ylabel("Eje Y")
plt.legend()
```

    <matplotlib.legend.Legend at 0x1242c25e0>

![png](output_10_1.png)

### API orientada a objetos

```python
fig = plt.figure(figsize=(10, 5), dpi=75)
ax = fig.gca()
ax.scatter(x, y1, label="Clase 1")
ax.scatter(x, y2, label="Clase 2")
ax.set_title("Datos artificiales")
ax.set_xlabel("Eje X")
ax.set_ylabel("Eje Y")
ax.legend()
```

    <matplotlib.legend.Legend at 0x1243ea520>

![png](output_12_1.png)

### Seaborn

```python
fig = plt.figure(figsize=(10, 5), dpi=75)
ax = fig.gca()
sns.scatterplot(x=x, y=y1, label="Clase 1", ax=ax)
sns.scatterplot(x=x, y=y2, label="Clase 2", ax=ax)
ax.set_title("Datos artificiales")
ax.set_xlabel("Eje X")
ax.set_ylabel("Eje Y")
ax.legend()
```

    <matplotlib.legend.Legend at 0x1244d8e20>

![png](output_14_1.png)

## Gráficas en 2D

Las gráficas más efectivas son las que muestran pocas variables y las relaciones que existen entre ellas. Mira el ejemplo de arriba, existe una relación entre `x` e `y1`, `y2`. Para graficar la relación, la entrada a nuestra función para graficar son los vectores `x` e `y1` y `x` e `y2`.

Usualmente, la variable **independiente** se coloca en el eje horizontal (el de las _equis_) mientras que la variable **dependiente** en el eje vertical (el de las _yes_).

```python
x = np.array([1, 2, 3, 4])
y = np.array([5, 6, 7, 8])
```

### Scatterplot

```python
fig = plt.figure(figsize=(7, 3), dpi=75)
ax = fig.gca()
sns.scatterplot(x=x, y=y, ax=ax)
```

    <AxesSubplot:>

![png](output_18_1.png)

### De barras

```python
fig = plt.figure(figsize=(7, 3), dpi=75)
ax = fig.gca()
ax.bar(x, y)
```

    <BarContainer object of 4 artists>

![png](output_20_1.png)

### Línea

```python
fig = plt.figure(figsize=(7, 3), dpi=75)
ax = fig.gca()
sns.lineplot(x=x, y=y)
```

    <AxesSubplot:>

![png](output_22_1.png)

## Stats

Como ya vimos un _stat_ es una forma de resumir la información presentada en una gráfica, entre estas operaciones tenemos:

- Binning: consiste en cateogrizar datos en un conjunto de _cubetas_
- Agregaciones estadísticas: promedio, mediana, desviación estandar...
- Suavizado y regresión: trata de encontrar una función que se aproxime a representar nuestro dataset

### Histogramas

```python
x = np.random.normal(0, 1, 300)
bins = np.linspace(-3, 3, 20)
```

```python
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
ax.hist(x, bins=bins)
ax.set_xlabel("Bin")
ax.set_ylabel("Count")
```

    Text(0, 0.5, 'Count')

![png](output_26_1.png)

```python
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
# sns.histplot(x = x, bins=bins, ax = ax, element="step", fill=False)
sns.histplot(x=x, bins=bins, ax=ax)
ax.set_xlabel("Bin")
ax.set_ylabel("Count")
```

    Text(0, 0.5, 'Count')

![png](output_27_1.png)

### Histogramas en 2D

<img src="/assets/04/spotify.png" />
<small>Mi año en Spotify <a href="https://tacosdedatos.com/mas-data-viz-con-spotify-python">https://tacosdedatos.com/mas-data-viz-con-spotify-python</a></small>

> Calculating optimal number of bins in a histogram [https://stats.stackexchange.com/a/862](https://stats.stackexchange.com/a/862)

### Agregaciones estadísticas

```python
flights = sns.load_dataset("flights")
flights.head()
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
      <th>year</th>
      <th>month</th>
      <th>passengers</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1949</td>
      <td>Jan</td>
      <td>112</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1949</td>
      <td>Feb</td>
      <td>118</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1949</td>
      <td>Mar</td>
      <td>132</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1949</td>
      <td>Apr</td>
      <td>129</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1949</td>
      <td>May</td>
      <td>121</td>
    </tr>
  </tbody>
</table>
</div>

```python
fig = plt.figure()
ax = fig.add_subplot(1, 1, 1)
sns.lineplot(data=flights, x="year", y="passengers", ax=ax)
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_31_1.png)

### Box plot

Es una gráfica interesantísima y muy útil que muestra múltiples **stats** de un conjunto de datos, se presenta en forma de un solo dibujo (**geom**).

Los valores mostrados son:

- **Rango intercuartil**, el rango entre los percentiles 25% y 75% del dataset
- **Mediana**
- **Extremos**, representanlos percentiles 2.5% y 97.5%
- **Outlier** o anomalías

```python
x = np.random.normal(0, 1, 100)
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
ax.boxplot(x, whis=[2.5, 97.5])
percentiles = np.percentile(x, [2.5, 25, 50, 75, 98.5])
min_x, max_x = np.min(x), np.max(x)


def annotate(y, text, left=False):
    offset = 80 if left else -130
    touch = 1.08 if left else 0.92
    ax.annotate(
        xy=(touch, y),
        text=text,
        xytext=(offset, 0),
        va="center",
        textcoords="offset points",
        arrowprops=dict(arrowstyle="->", color="C2", alpha=0.5),
    )


annotate(min_x, "Anomalía (mínimo)")
annotate(max_x, "Anomalía (máximo)")
annotate(percentiles[0], "Extremo menor", True)
annotate(percentiles[1], "Percentil 25%", True)
annotate(percentiles[2], "Mediana")
annotate(percentiles[3], "Percentil 75%", True)
annotate(percentiles[4], "Extremo mayor", True)
ax.set_xlabel("Valores")
ax.set_ylabel("Eje X")
ax.set_title("Box plot")
```

    Text(0.5, 1.0, 'Box plot')

![png](output_33_1.png)

```python
# Con seaborn
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
sns.boxplot(x="month", y="passengers", data=flights, ax=ax)
ax.set_xlabel("Mes")
ax.set_ylabel("Pasajeros")
ax.set_title("Vuelos")
```

    Text(0.5, 1.0, 'Vuelos')

![png](output_34_1.png)

### Violin plot

Son una extensión de las Box Plots que muestran la distribución de los datos de forma precisa usando una función llamada _Kernel Density Estimate_:

```python
# Con seaborn
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
sns.violinplot(x="month", y="passengers", data=flights, ax=ax)
ax.set_xlabel("Mes")
ax.set_ylabel("Pasajeros")
ax.set_title("Vuelos")
```

    Text(0.5, 1.0, 'Vuelos')

![png](output_36_1.png)

### Regresión

La regresión es una operación que involucra el encontrar una función que pueda representar nuestros datos.

```python
tips = sns.load_dataset("tips")
plt.figure(figsize=(10, 5), dpi=75)
ax = sns.regplot(x="total_bill", y="tip", data=tips)
ax.set_xlabel("Total cuenta")
ax.set_ylabel("Propina")
```

    Text(0, 0.5, 'Propina')

![png](output_38_1.png)

### Smoothing

De manera similar, el suavizado encuentra una versión simplificada para representar nuestros datos.

## Geoms

### Marcadores o _Markers_

Los _markers_ son un ejemplo de _geom_ que podemos usar para representar puntos en la gráfica. Los podemos usar para dotar de información a nuestra gráfica:

```python
seasons = {
    "Invierno": ["Dec", "Jan", "Feb"],
    "Primavera": ["Mar", "Apr", "May"],
    "Verano": ["Jun", "Jul", "Aug"],
    "Otoño": ["Sep", "Oct", "Nov"],
}
seasons_months = {
    season: month for month, seasons in seasons.items() for season in seasons
}
flights["season"] = flights["month"].map(seasons_months)
agg_flights = flights.groupby(["year", "season"])["passengers"].sum().reset_index()
vuelos = {
    season: agg_flights.query(f"season == '{season}'") for season in seasons.keys()
}
```

```python
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
ax.scatter(
    vuelos["Invierno"]["year"],
    vuelos["Invierno"]["passengers"],
    label="Invierno",
    marker="+",
    color="black",
)
ax.scatter(
    vuelos["Primavera"]["year"],
    vuelos["Primavera"]["passengers"],
    label="Primavera",
    marker="X",
    color="black",
)
ax.scatter(
    vuelos["Verano"]["year"],
    vuelos["Verano"]["passengers"],
    label="Verano",
    marker="*",
    color="black",
)
ax.scatter(
    vuelos["Otoño"]["year"],
    vuelos["Otoño"]["passengers"],
    label="Otoño",
    marker="$❄$",
    color="black",
)
ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_43_1.png)

Revisa la documentación de matplotlib sobre los marcadores disponibles: [Marker reference](https://matplotlib.org/stable/gallery/lines_bars_and_markers/marker_reference.html).

```python
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
ax.scatter(
    vuelos["Invierno"]["year"],
    vuelos["Invierno"]["passengers"],
    label="Invierno",
    marker="+",
    color="blue",
)
ax.scatter(
    vuelos["Primavera"]["year"],
    vuelos["Primavera"]["passengers"],
    label="Primavera",
    marker="X",
    color="green",
)
ax.scatter(
    vuelos["Verano"]["year"],
    vuelos["Verano"]["passengers"],
    label="Verano",
    marker="*",
    color="orange",
)
ax.scatter(
    vuelos["Otoño"]["year"],
    vuelos["Otoño"]["passengers"],
    label="Otoño",
    marker="$❄$",
    color="brown",
)
ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_45_1.png)

### Usando marcadores para mostrar más variables

#### Terremotos en México

(Tal vez no es el mejor ejemplo, pero vamos a tratar de sacarle jugo) Te invito a ver otra visualización que hice con este mismo dataset en [Tacos De Datos](https://tacosdedatos.com/visualizando-temblores-joyplots).

```python
terremotos = pd.read_csv(
    "assets/04/SSNMX_catalogo_20170101_20180101.csv",
    engine="python",
    skiprows=4,
    skipfooter=7,
)
terremotos = terremotos[["Latitud", "Longitud", "Magnitud", "Profundidad"]]
terremotos = terremotos.query("Magnitud > 2.0")
terremotos = terremotos.query("Latitud < 22.0")
terremotos.head()
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
      <th>Latitud</th>
      <th>Longitud</th>
      <th>Magnitud</th>
      <th>Profundidad</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15.2218</td>
      <td>-93.1245</td>
      <td>3.5</td>
      <td>81.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>16.9610</td>
      <td>-99.5348</td>
      <td>3.2</td>
      <td>36.7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>16.5082</td>
      <td>-98.5332</td>
      <td>3.4</td>
      <td>18.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>14.5250</td>
      <td>-92.5765</td>
      <td>3.7</td>
      <td>54.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>16.3272</td>
      <td>-98.2833</td>
      <td>3.5</td>
      <td>8.6</td>
    </tr>
  </tbody>
</table>
</div>

```python
fig = plt.figure(figsize=(7, 6), dpi=150)
ax = fig.add_subplot(1, 1, 1)
ax.scatter(terremotos["Longitud"], terremotos["Latitud"])
ax.set_title("Terremotos en México")
ax.set_xlabel("Longitud")
ax.set_ylabel("Latitud")
```

    Text(0, 0.5, 'Latitud')

![png](output_49_1.png)

```python
def add_magnitud_legend(ax, etiquetas, magnitudes_reales):
    ls = []
    for scale in magnitudes_reales:
        l = ax.scatter([], [], s=scale, edgecolors="none", facecolors="C0")
        ls.append(l)
    leg = ax.legend(
        ls,
        etiquetas,
        ncol=len(etiquetas),
        frameon=True,
        fontsize=12,
        handlelength=2,
        loc=8,
        borderpad=1,
        handletextpad=1,
        title="Magnitud",
        scatterpoints=1,
    )
```

```python
fig = plt.figure(figsize=(8, 6), dpi=150)
ax = fig.add_subplot(1, 1, 1)

magnitudes = np.linspace(4, 7, 4)
magnitudes_reales = (magnitudes[:-1] - 3) * 30
etiquetas = [f">{number:.2f}" for number in magnitudes[:-1]]


puntos = ax.scatter(
    terremotos["Longitud"],
    terremotos["Latitud"],
    s=(terremotos["Magnitud"] - 3) * 30,
    alpha=0.2,
)

ax.set_title("Terremotos en México")
ax.set_xlabel("Longitud")
ax.set_ylabel("Latitud")
ax.set_ylim((terremotos["Latitud"].min() - 2, terremotos["Latitud"].max() + 0.5))

add_magnitud_legend(ax, etiquetas, magnitudes_reales)
```

    /Users/antonioferegrino/.local/share/virtualenvs/df-JhqtXTwc/lib/python3.8/site-packages/matplotlib/collections.py:1003: RuntimeWarning: invalid value encountered in sqrt
      scale = np.sqrt(self._sizes) * dpi / 72.0 * self._factor

![png](output_51_1.png)

```python
fig = plt.figure(figsize=(8, 6), dpi=150)
ax = fig.add_subplot(1, 1, 1)

magnitudes = np.linspace(4, 7, 4)
magnitudes_reales = (magnitudes[:-1] - 3) * 30
etiquetas = [f">{number:.2f}" for number in magnitudes[:-1]]


puntos = ax.scatter(
    terremotos["Longitud"],
    terremotos["Latitud"],
    s=(terremotos["Magnitud"] - 3) * 30,
    c=terremotos["Profundidad"],
)

ax.set_facecolor("black")
ax.set_title("Terremotos en México")
ax.set_xlabel("Longitud")
ax.set_ylabel("Latitud")
ax.set_ylim((terremotos["Latitud"].min() - 2, terremotos["Latitud"].max() + 0.5))
cb = fig.colorbar(puntos)
cb.set_label("Kilómetros")

add_magnitud_legend(ax, etiquetas, magnitudes_reales)
```

![png](output_52_0.png)

### Mapas de colores

<img src="/assets/04/unsigned.jpg" />

<img src="/assets/04/unsigned-2d.jpg" />

<img src="/assets/04/signed.jpg" />

<img src="/assets/04/categories.jpg" />

<img src="/assets/04/misc.jpg" />

Enlace a las gráficas [https://matplotlib.org/2.0.2/examples/color/colormaps_reference.html](https://matplotlib.org/2.0.2/examples/color/colormaps_reference.html)

### Más colores

Uno puede definir sus propios mapas de colores, esta página te pude servir como inspiración para crear tus propias paletas [https://colorbrewer2.org](https://colorbrewer2.org).

```python
fig = plt.figure(figsize=(8, 6), dpi=150)
ax = fig.add_subplot(1, 1, 1)

magnitudes = np.linspace(4, 7, 4)
magnitudes_reales = (magnitudes[:-1] - 3) * 30
etiquetas = [f">{number:.2f}" for number in magnitudes[:-1]]


puntos = ax.scatter(
    terremotos["Longitud"],
    terremotos["Latitud"],
    s=(terremotos["Magnitud"] - 3) * 30,
    c=terremotos["Profundidad"],
    cmap="YlOrRd",
)

ax.set_facecolor("black")
ax.set_title("Terremotos en México")
ax.set_xlabel("Longitud")
ax.set_ylabel("Latitud")
ax.set_ylim((terremotos["Latitud"].min() - 2, terremotos["Latitud"].max() + 0.5))
cb = fig.colorbar(puntos)
cb.set_label("Kilómetros")

add_magnitud_legend(ax, etiquetas, magnitudes_reales)
```

![png](output_54_0.png)

#### Uso de colores

> 🚨 Ten en cuenta que a veces las gráficas que hagas pueden ser impresas en escala de grises o blanco y negro.

> 🚨 Ten en cuenta hay personas con alguna deficiencia en la percepción de colores: es decir, a veces los colores no lo son todo en las gráficas.

### Líneas

Son otra forma de **geoms** que unen puntos. Usamos líneas cuando queremos representar que hay algo entre dos puntos (una relación, por ejemplo).

#### Diferentes estilos de líneas

Podemos usar el grosor (`linewidth`) y color (`color`) de una línea para diferenciar entre distintas variables:

```python
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
ax.plot(
    vuelos["Invierno"]["year"],
    vuelos["Invierno"]["passengers"],
    label="Invierno",
    marker="+",
    color="blue",
    linewidth=0.5,
)
ax.plot(
    vuelos["Primavera"]["year"],
    vuelos["Primavera"]["passengers"],
    label="Primavera",
    marker="X",
    color="green",
    linewidth=1,
)
ax.plot(
    vuelos["Verano"]["year"],
    vuelos["Verano"]["passengers"],
    label="Verano",
    marker="*",
    color="orange",
    linewidth=2,
)
ax.plot(
    vuelos["Otoño"]["year"],
    vuelos["Otoño"]["passengers"],
    label="Otoño",
    marker="x",
    color="brown",
    linewidth=4,
)
ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_58_1.png)

Podemos usar distintos estilos de líneas (`linestyle`):

```python
fig = plt.figure(figsize=(10, 5), dpi=100)
ax = fig.gca()

ax.plot(
    vuelos["Invierno"]["year"],
    vuelos["Invierno"]["passengers"],
    label="Invierno",
    marker="+",
    color="blue",
    linestyle="--",
    linewidth=2,
)

ax.plot(
    vuelos["Primavera"]["year"],
    vuelos["Primavera"]["passengers"],
    label="Primavera",
    marker="X",
    color="green",
    linestyle=":",
    linewidth=2,
)
ax.plot(
    vuelos["Verano"]["year"],
    vuelos["Verano"]["passengers"],
    label="Verano",
    marker="*",
    color="orange",
    linestyle="-",
    linewidth=2,
)
ax.plot(
    vuelos["Otoño"]["year"],
    vuelos["Otoño"]["passengers"],
    label="Otoño",
    marker="$❄$",
    color="brown",
    linestyle="-.",
    linewidth=2,
)
ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_60_1.png)

```python
fig = plt.figure(figsize=(10, 5), dpi=100)
ax = fig.gca()

sns.lineplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    ci=None,
    hue="season",
    ax=ax,
)

ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_61_1.png)

Podemos usar transparencia `alpha` para resaltar elementos que merezcan llamar la atención:

```python
fig = plt.figure(figsize=(10, 5), dpi=100)
ax = fig.gca()

mean = pd.DataFrame(
    {season: vuelos[season]["passengers"].reset_index(drop=True) for season in seasons}
).mean(axis=1)

ax.plot(
    vuelos["Otoño"]["year"],
    mean,
    label="Promedio",
    marker="$o$",
    color="black",
    linestyle="-",
    linewidth=2,
)

ax.plot(
    vuelos["Invierno"]["year"],
    vuelos["Invierno"]["passengers"],
    label="Invierno",
    marker="+",
    color="blue",
    linestyle="--",
    linewidth=2,
    alpha=0.2,
)
ax.plot(
    vuelos["Primavera"]["year"],
    vuelos["Primavera"]["passengers"],
    label="Primavera",
    marker="X",
    color="green",
    linestyle=":",
    linewidth=2,
    alpha=0.2,
)
ax.plot(
    vuelos["Verano"]["year"],
    vuelos["Verano"]["passengers"],
    label="Verano",
    marker="*",
    color="orange",
    linestyle="-",
    linewidth=2,
    alpha=0.2,
)
ax.plot(
    vuelos["Otoño"]["year"],
    vuelos["Otoño"]["passengers"],
    label="Otoño",
    marker="$❄$",
    color="brown",
    linestyle="-.",
    linewidth=2,
    alpha=0.2,
)

ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_63_1.png)

### Gráficas de barras

```python
fig = plt.figure(figsize=(10, 5), dpi=100)
ax = fig.gca()

sns.barplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    hue="season",
    ci=None,
    ax=ax,
)

ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_65_1.png)

## Coords

La forma de visualizar las **coords** en una gráfica hecha con matplotlib o alguna de sus derivadas, es a través de los ejes o `axes`.

Estos ejes especifican un rango en unidades usadas para medir nuestras variables dentro del dataset (número de pasajeros) y lo presentan en un rango determinado en unidades visuales (pulgadas).

Ya hemos visto cómo es que podemos configurar el tamaño de nuestras gráficas, e indirectamente, los valores de conversión de nuestros ejes

### Cambio de tamaño

#### Gráfica 🚼

```python
fig = plt.figure(figsize=(4, 2), dpi=100)
ax = fig.gca()

sns.lineplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    ci=None,
    hue="season",
    ax=ax,
)

ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_70_1.png)

#### Gráfica grande

```python
fig = plt.figure(figsize=(10, 7), dpi=300)
ax = fig.gca()

sns.lineplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    ci=None,
    hue="season",
    ax=ax,
)

ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_72_1.png)

### Cambio de rangos

Podemos elegir el rango de datos que se muestra en nuestra gráfica, podemos usar `set_ylims` y `set_xlim` (recuerda que debes utilizar los valores correspondientes al rango en tu dataset):

```python
fig = plt.figure(figsize=(10, 7), dpi=100)
ax = fig.gca()
sns.lineplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    ci=None,
    hue="season",
    ax=ax,
)

ax.set_xlim((1950, 1952))  # de 1950 a 1952
ax.set_ylim((300, 800))  # de 300 a 800 pasajeros

ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_74_1.png)

### Relación de aspecto

Podemos usar `set_aspect` para especificar un número llamado `ratio` el cual determinará la siguiente operación $altura = ratio \times anchura$.

```python
# Un círculo
angle = np.linspace(-np.pi, np.pi, 100)
x, y = np.cos(angle), -np.sin(angle)

fig, axs = plt.subplots(1, 3, figsize=(12, 3))

fig = plt.figure(figsize=(12, 3))
axs[0].plot(x, y)
axs[0].set_title("Relación de aspecto por default")

axs[1].plot(x, y)
axs[1].set_aspect(0.5)
axs[1].set_title("Relación incorrecta")


axs[2].plot(x, y)
axs[2].set_aspect(1.0)
axs[2].set_title("¡Correcta!")
```

    Text(0.5, 1.0, '¡Correcta!')

![png](output_76_1.png)

    <Figure size 864x216 with 0 Axes>

## Layers

Ya hemos estado haciendo uso de **layers** a lo largo de este notebook. Cada vez que agregamos una nueva colección de **geoms** a un _axes_, estamos agregando una nueva capa:

```python
fig = plt.figure(figsize=(10, 5), dpi=150)
ax = fig.gca()
ax.plot(
    vuelos["Invierno"]["year"],
    vuelos["Invierno"]["passengers"],
    label="Invierno",
    marker="+",
    color="blue",
    linewidth=0.5,
)
ax.plot(
    vuelos["Primavera"]["year"],
    vuelos["Primavera"]["passengers"],
    label="Primavera",
    marker="X",
    color="green",
    linewidth=1,
)
ax.plot(
    vuelos["Verano"]["year"],
    vuelos["Verano"]["passengers"],
    label="Verano",
    marker="*",
    color="orange",
    linewidth=2,
)
ax.plot(
    vuelos["Otoño"]["year"],
    vuelos["Otoño"]["passengers"],
    label="Otoño",
    marker="$❄$",
    color="brown",
    linewidth=4,
)
ax.legend()
ax.set_title("Pasajeros por temporada")
ax.set_xlabel("Año")
ax.set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_78_1.png)

## Facets

Podemos además usar **facets** para mostrar difetentes layers dentro de una misma figura, la siguiente es solo una forma de crear distintos facets, en _matplotlib_ se les conoce como _subplots_:

```python
fig, axs = plt.subplots(1, 2, figsize=(12, 5))

sns.barplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    hue="season",
    ci=None,
    ax=axs[0],
)

axs[0].legend()
axs[0].set_title("Pasajeros por temporada")
axs[0].set_xlabel("Año")
axs[0].set_ylabel("Pasajeros")


sns.lineplot(
    x="year",
    y="passengers",
    data=flights,
    estimator=np.sum,
    hue="season",
    ci=None,
    ax=axs[1],
)
axs[1].legend()
axs[1].set_title("Pasajeros por temporada")
axs[1].set_xlabel("Año")
axs[1].set_ylabel("Pasajeros")
```

    Text(0, 0.5, 'Pasajeros')

![png](output_80_1.png)

Échale un ojo a la documentación de _matplotlib_ para encontrar más código e inspiración [https://matplotlib.org/stable/tutorials/intermediate/gridspec.html](https://matplotlib.org/stable/tutorials/intermediate/gridspec.html)

## Hacicéndo una buena gráfica

Es tu momento para brillar, encuentra un dataset (o usa alguno de los que están en este Notebook) y crea una gráfica. Mándamela por Twitter o al Discord para revisarla y compartirla 😁

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
                <a href="https://youtu.be/7vZY6JdswKs">
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

- **How to lie with statistics** de Darrell Huff: [México](https://amzn.to/3duQLnf) &middot; [España](https://amzn.to/3rIwcst) · [US](https://amzn.to/3rMFALK)
- **How charts lie** de Alberto Cairo: [México](https://amzn.to/3cNG14s) &middot; [España](https://amzn.to/3ukvrb2) · [US](https://amzn.to/3rMGp7i)
- **The grammar of graphics** - [https://byrneslab.net/classes/biol607/readings/wickham_layered-grammar.pdf](https://byrneslab.net/classes/biol607/readings/wickham_layered-grammar.pdf)
- **Los libros de Edward Tufte** - https://www.edwardtufte.com/tufte/books_vdqi

### Videos

- **matplotlib (Python Plotting Library) Beginner | SciPy 2016 Tutorial | Nicolas Rougier** - [https://www.youtube.com/watch?v=p7Mj-4kASmI](https://www.youtube.com/watch?v=p7Mj-4kASmI)

### Sitios web

- **PyViz Overviews** - [https://pyviz.org/overviews/index.html](https://pyviz.org/overviews/index.html)
- **Dr. Randal S. Olson's data visualizations** - [http://www.randalolson.com/category/data-visualization/](http://www.randalolson.com/category/data-visualization/)
- **Python Graph Gallery** - [https://www.python-graph-gallery.com/](https://www.python-graph-gallery.com/)
- **Choosing a good chart** - [https://extremepresentation.typepad.com/blog/2006/09/choosing_a_good.html](https://extremepresentation.typepad.com/blog/2006/09/choosing_a_good.html)
- **A legendar** - [https://towardsdatascience.com/murdering-a-legendary-data-story-what-can-we-learn-from-a-grammar-of-graphics-ad6ca42f5e30](https://towardsdatascience.com/murdering-a-legendary-data-story-what-can-we-learn-from-a-grammar-of-graphics-ad6ca42f5e30)
- **Ten Simple Rules for Better Figures** - [https://hal.inria.fr/hal-01063732/document](https://hal.inria.fr/hal-01063732/document)
- **A Tour Through the Visualization Zoo** - [https://homes.cs.washington.edu/~jheer//files/zoo/](https://homes.cs.washington.edu/~jheer//files/zoo/)
- **Effectively Using Matplotlib** - [https://pbpython.com/effective-matplotlib.html](https://pbpython.com/effective-matplotlib.html)
- **Matplotblog** - [https://matplotlib.org/matplotblog/](https://matplotlib.org/matplotblog/)
- **Matplotlib - Gallery** - [https://matplotlib.org/stable/gallery/index.html](https://matplotlib.org/stable/gallery/index.html)
