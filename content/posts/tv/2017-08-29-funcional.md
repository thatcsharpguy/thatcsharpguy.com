---
title:  La programación funcional
date:  2017-08-29 18:00:00
youtube:  pdbkwUDdfRk
images_folder:  /tv/functional/
summary:  Como su nombre lo indica, está centrada en funciones, las cuales son tratadas como ciudadanos de primera clase.
featured_image:  featured.png
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

La programación funcional es otro paradigma de programación, sí, justo como la programación reactiva o la orientada a objetos. Esta tiene sus raices fuertemente cimentadas en el cálculo lambda, introducido en 1930 por Alonzo Church, pero fue hasta 1958, con la especificación de Lisp que surgió el primer lenguaje con una norción funcional. Este paradigma de programación estuvo generalmente relegado a la academia, sin embargo, en la actualidad, está comenzando a abarcar más terreno en otras áreas.

Como su nombre lo indica, está centrada en funciones, las cuales son tratadas como ciudadanos de primera clase. En este esquema, la función siempre representará la relación entre una entrada y una salida, tal como en matemáticas, en las que tienes que si a una función introduces un valor X, obtendrás como resultado un valor Y.

<img src="/images/tv__functional__function.png" title=""Function"" />

Y es aquí cuando aparece otro concepto conocido como las funciones de orden superior, que son aquellas funciones que pueden tomar otras funciones como argumentos o retornar otras funciones como resultado de su ejecución.

Como ejemplo toma estas tres funciones que son de las más populares en la programación funcional:

<img src="/images/tv__functional__mapreducefilter.png" title=""Map reduce filter"" />

Que son funciones que toman como argumento otra función. En el caso de `map` que recibe una función que especifica la transformación de un valor a otro. El caso de `reduce` que nos sirve para aplicar una función de acumulación a una coleccion de elementos, y `filter` que toma una función que especifica los criterios de elegibilidad de los elementos de una colección. En el blog tengo un post al respecto de estas funciones, <a href="..\..\post\map-filter-reduce-c-sharp" target="_blank">uno en C#</a> y <a href="..\..\post\map-filter-reduce-f-sharp" target="_blank">uno en F#</a>, por si quieren echarle un ojo a código.

En el más estricto sentido, los lenguajes de programación puramente funcionales tienen algunas características que los hacen tan únicos:  

- **No hay ciclos.** La programación funcional hace uso de la recursividad, que como ya vimos, es una herramienta muy poderosa cuando de expresar problemas se trata.
- **No hay variables ni asignaciones… o bueno, mejor dicho: inmutabilidad.** Una vez que un valor ha sido establecido y almacenado este no puede ser cambiado a lo largo de la ejecución del programa del bloque del programa en el que fue definido.
- **No tienen estados, evita los efectos colaterales.** En la programación funcional, el llamar a una función multiples veces con las mismas entrdas siempre devolverá los mismos resultados, estos no se verán influenciados por condiciones externas o estados almacenados previamente.

A esto último también se le llega a conocer como *transparencia referencial*, que básicamente significa que podemos reemplazar cualquier referencia a una función por el valor que regresa sin que se altere el resultado o el comportamiento del programa.

La realidad es que muchos de los lenguajes que seguramente conoces, o conoceras, no son puramente funcionales. Muchos de ellos retienen nociones de otros paradigmas, como los ciclos, y las variables y a mi modo de ver las cosas, me parece algo bastante bueno, al menos para facilitar la trancisión y el aprendizaje para los desarrolladores que estamos acostumbrados a  la forma de programar con lenguajes como C# que son más orientados a objetos.

Las grandes ventajas de usar la programación funcional son:

- El poder ejecutar nuestros programas en entornos que favorecen la ejecución paralela, por aquello de que no existe la posibilidad de que el resultado de una función se vea afectada por elementos externos a ella.

- Y en general un código más reducido, puesto que se le especifica al programa qué es lo que tiene que hacer y no cómo hacerlo.

Compara estos dos bloques, ambos en C#, an ambos estamos buscando valores dentro de una colección. Mientras que del modo tradicional le estamos diciendo al lenguaje que tiene que recorrer el arreglo y buscar el valor, del otro solamente le estamos diciendo que tiene que buscarlo.

<img src="/images/tv__functional__codecompare.png" title=""Code compare"" />

Esto, como puedes ver es otro de esos traslapes de paradigmas de programación en lenguajes, así como hay lenguajes orientados a funciones que retienen conceptos de otros paradigmas, también hay lenguajes orientados a objetos que ya adoptan nociones de la programación funcional. 

<img src="/images/tv__functional__overlay.png" title=""Languages"" />

Si quieres ver un poco más sobre cómo podemos usar LINQ para llevar la programación funcional a C#, visita <a href="..\..\tag\LINQ" target="_blank">estos posts</a>.