---
title:  Azúcar Sintáctica
date:  2016-05-04 18:00:00
youtube_id: Ypgw-TgT70I
slug: azucar-sintactica
images_folder:  /tv/azucar-sintactica/
categories:  c-sharp
summary:  El azúcar sintáctico no es más que eso, un endulzante que en teoría debe mejorar nuestra experiencia cuando escribimos código.
featured_image:  featured.png
tags:  Meta, YouTube
featured_tag:  Tv
layout: video
---

> El azúcar sintáctico no es más que eso: un endulzante que en teoría debe mejorar nuestra experiencia cuando escribimos código.

La creación del término se le atribuye a Peter J. Landin quién en 1964 se <a href=" http://www.cs.cmu.edu/~crary/819-f09/Landin64.pdf" target="_blank" rel="nofollow">refirió así</a> a la sintaxis de un lenguae de programación en donde se podía intercambiar el símbolo lambda (λ) por la palabra "where":

<img src="https://thatcsharpguy.github.io/postimages/tv/azucar-sintactica/landin.png" title=""Origen de la azúcar sintáctica"" />

A partir de ahí se han creado varias definiciones, pero podríamos decir que **son instrucciones no esenciales del lenguaje**, que usualmente se corresponden con una o varias instrucciones que sí son esenciales. Es importante señalar que a veces con una sola "instrucción azucarada", se ejecutan varias instrucciones normales.

Es decir que la azúcar sintáctica es aquella que podremos intercambiar en el código fuente de un programa por instrucciones básicas sin que su funcionamiento y comportamiento se vea afectado.

## ¿Por qué existen?

Puede que te estés preguntando por qué existen si no son esenciales. Pues bien, su existencia responde a tres principales puntos, en los que creo que vas a estar de acuerdo:

- Hacen más humanamente legible el código fuente de un programa, haciendo referencia a la frase que dice que "los programas deben ser escritos para que los humanos los lean y, solo ocasionalmente, para que las computadoras lo ejecuten" 

- Además de hacer más legible el código, también facilitan su escritura ya que, como dije antes, a veces son atajos a una o varias instrucciones más complejas.

- Sin duda esta es una consecuencia de los dos puntos anteriores: si es más fácil de leer y escribir, a nosotros los desarrolladores nos vuelve más productivos.

## ¿Cómo funcionan?

Al estar definidos dentro del lenguaje mismo, es tarea del preprocesador, el analizador sintáctico, del compilador, o alguna otra herramienta similar, el convertirlas a algo que el procesador de la computadora pueda ejecutar.

Cuando alguna de estas herramientas se encuentra con una de estas instruccines, la transforma, en algunos casos la expande en alguna de esas construcciones básicas del lenguaje para que después sea evaluado como tal. **Al final todo termina siendo 1's y 0's**.

## Ejemplos

Ejemplos hay muchos, comenzando por el que inicialmente inspiró la creación del término: Reemplazar el símbolo lambda por la palabra *where*.

- En C, `a[i]` es azúcar para `*(a + i)`
- La palabra `var` en C#
- El operador `+=` en varios lenguajes
- La asignación paralela (Destructuring assignment) en JavaScript
- El condicional `unless` que es como un `if` negado

## Extremismo

Como todo en este mundo, respecto a este tema también hay extremismo, y es que retomando el dicho de que al final todo es 1's y 0's podemos ver a un lenguaje de programación de alto nivel como la azúcar sintáctica de otro de bajo nivel:

El lenguaje ensamblador es azúcar sintáctica para el código máquina, C para ensamblador, C++ para C, Java, C# y Objective-C para C++ y cosas de ese tipo.

Además del extremismo, también existen críticas con respecto a su existencia y uso.

Algunos programadores sugieren que no es necesario que un lenguaje cuente con este tipo de sintaxis. Otros dicen que solo complica más las cosas al permitir que exista más de una forma de escribir una instrucción, haciendo que un lenguaje se vuelva más complicado.

Inclusive hay quienes dicen que fomentan la escritura de mal código, sin ser de aquellos que dicen que si programas usando alguna instrucción que caiga en esta categoría, no eres un buen programador o programadora, lo cierto es que sí pueden llegar a facilitar escribir mál código si no conoces plenamente lo que realiza la instrucción que escribiste en el código.

#### Mi sugerencia

Yo no estoy para nada en contra de la existencia de este tipo de sintaxis, por el contrario, las uso y es que la que existe en C# simplifica bastante la escritura y lectura de las aplicaciones. 

Eso sí, creo que debes conocer qué es lo que realmente hace la instrucción que estás usando, inclusive conocer la manera "compleja" de realizar la acción que intentas lograr con dicha instrucción.

También creo que debe usarse con moderación, porque de pronto se puede terminar con un código bastante difícil de leer y en algunos casos inclusive difícil de entender por el compilador.

Para mi, lo correcto es un balance entre usar o no.