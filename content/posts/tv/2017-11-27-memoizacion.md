---
title:  Memoización
date:  2017-11-27 18:00:00
youtube_id: D_V7-K-BPXo
images_folder:  /tv/memoization/
summary:  La memoización es una técnica de optimización de algoritmos que permite acelerar la ejecución de una función.
featured_image:  featured.png
tweet_id:  935202027583483904
tags:  Meta, YouTube
featured_tag:  Tv
layout: video
---

Sí, memoización, no, no memorización.

La memorización es una técnica de optimización  usada en la programación que consiste en almacenar los resultados de funciones previamente calculadas para evitar ejecutarlas de nuevo cuando las necesitemos, en otras palabras, es básicamente "cachear" los resultados.

Toma por ejemplo el cálculo de la sucesión de fibonacci, que como vimos, en un video anterior. Esto definido así:

```
Fib(0) = Fib(1) = 1
Fib(n) = Fib(n-1) + Fib(n-2)
```

Como puedes ver, para el fibonacci de 5, llega un momento en el que empezamos a calcular valores que ya tenemos, y visto de esta manera estamos únicamente calculando 5 valores diferentes de las 13 operaciones que en "realidad" tendríamos que hacer.

Así que, ¿que pasa si después de calcularlo la primera vez, lo almacenamos en una tabla o en un arreglo? Así, la siguiente vez que el valor sea requerido, podemos buscar si ya existe, y si sí, devolvemos el valor ya calculado. De otro modo vamos a tener que ejecutar la función y todas las que esta llame, nuevamente.

Esto funciona bastante bien cuando el costo de ejecutar una función es muy alto, y se desperdiciarían recursos calculando valores que ya hemos calculado previamente. La memorización reduce el tiempo de ejecución a costa del espacio de almacenamiento, ya que, como vimos, para evitar calcular almacena los valores. Y es benéfica cuando de atacar problemas cuyas subsoluciones se deben resolver repetidamente, con la gran ventaja de solo calcular algunas cosas unas cuantas veces.

Existen lenguajes que soportan y proveen una forma sencilla de implementar esta técnica, como a través de un decorador o una palabra clave... sin embargo existen otros en los que esta se tiene que implementar manualmente mediante el uso de alguna estructura de datos como un arreglo o un árbol binario.

Además, la memorización juega un rol importante en la técnica de programación conocida como programación dinámica, de la cual muy pronto haré un video.