---
title:  Ciudadanos de primera clase
date:  2016-07-20 18:00:00
youtube_id: OoWKSgrnTwc
images_folder:  /tv/first-class-citizens/
categories:  c-sharp
summary:  En programación, cuando se habla de ciudadanos de primera clase es para hacer referencia a un elemento del lenguaje que posee la mayor cantidad de privilegios.
featured_image:  fcc.jpg
tweet_id:  755936816021270532
tags:  Meta, Tv
featured_tag:  Tv
layout: video
---

En programación, cuando se habla de ciudadanos de primera clase o *First-class citizens* (o *first-class constructs* o *first-class elements*), es para hacer referencia a un elemento del lenguaje que posee la mayor cantidad de privilegios dentro de dicho lenguaje.  

En 1967 <a href="https://en.wikipedia.org/wiki/Christopher_Strachey" target="_blank">Christopher Strachey</a> hizo referencia a los terminos objeto de primera y segunda clase para referirse a la diferencia entre un número real y un procedimiento, en términos del uso que podía tener cada uno de ellos en ALGOL.

<img src="/images/tv__first-class-citizens__document.jpg" title="Document" />

Consulta el documento completo en <a href="http://www.itu.dk/courses/BPRD/E2013/fundamental-1967.pdf" target="_blank">este enlace</a>.

A pesar de lo anterior, este no es un término bien definido, ya que varía por muchos factores, y el principal de ellos es el lenguaje de programación del cual estemos hablando. Pero hay ciertas características que generalmente se toman en cuenta para decir si un elemento es de primera clase o no:

 - Puede ser guardado en variables
 - Pueden ser usados como argumentos
 - Pueden ser un valor de retorno
 - Tienen identidad propia    

Es importante señalar que si bien "existen" ciudadanos de primera clase, no existen de segunda o tercera. Una cosa es de primera o no lo es.

Por poner un ejemplo de cosas que son y no son, podemos decir que los `Enums` de C# no son de primera clase, ya que básicamente es azúcar sintáctica para referirnos a un número entero.

Por otro lado, desde C# 3.0 podemos crear funciones, mediante `Func` o `Action` y tratarlas como de primera clase.