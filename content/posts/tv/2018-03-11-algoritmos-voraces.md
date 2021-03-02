---
title:  Los algoritmos voraces
date:  2018-03-11 18:00:00
youtube_id: DsS33DCWvC4
summary:  La idea detrás de los algoritmos voraces (o greedy) consiste en siempre tomar la mejor decisión de todas las que puede tomar inmediatamante con la esperanza de que al juntar todas estas pequeñas "mejores" decisiones, se obtendrá la mejor solución al problema en general.
tweet_id:  973684466211282945
lang:  en
tags:  Meta, YouTube
featured_tag:  Tv
layout: video
---

Los algoritmos voraces o *greedy* son algoritmos que implementan una heuristica(técnica) que tiene como objetivo optimizar la búsqueda de una solución óptima para un problema.

La idea detrás de los algoritmos voraces (o greedy) consiste en siempre tomar la mejor decisión de todas las que puede tomar en ese momento, con la esperanza de que al juntar todas estas pequeñas mejores decisiones, se obtendrá la mejor solución al problema en general.

Seguramente verán esto explicado como que el algoritmo toma decisiones localmente óptimas con la esperanza de que esto lo llevará a la solución solución globalmente óptima.

Toma por ejemplo este problema, supón que existen estas ciudades, y tu tarea es gastar lo menos posible en llegar de Pueblo Paleta a X, a Y, como puedes ver, hay muchas rutas. Un algoritmo voraz haría algo como esto:

A cada paso, comprar el boleto más barato hasta llegar al pueblo Y.

### No siempre es lo mejor

Como lo dije, el algoritmo tiene la esperanza de que al hacer esto va a obtener el mejor resultado al final, pero esto no necesariamente es así.

Imagina un escenario como el anterior, pero con los precios un poco diferentes. Siguiendo la heurística voraz, un algoritmo haría algo como esto:

Tomando nuevamente el boleto más barato, aunque al final, estas decisiones localmente óptimas lo llevaron a gastar más dinero.


### ¿Por qué existen?  
Pero bueno, si al final puede que no obtenga el mejor resultado, ¿por qué existe esta técnica?

Pues hay problemas para los calcular la solución globalmente óptima es algo inimaginable, o increíblemente difícil como es el caso del problema del agente viajero (del cuál les hablé en un video anterior). Y tu como desarrollador tienes que tomar la decisión entre sacrificar un poco la optimalidad de tu algoritmo por el tiempo de procesamiento.

También existen porque en general son sencillos de entender y también sencillos de programar.
 
Recuerda que no existe un martillo dorado que sea la solución a todos los problemas que te encuentres, y que este esta es una de esas herramientas que puedes usar.

### Los componentes 
Ahora, no todos los problemas son susceptibles de afrontarse con este tipo de algoritmos, para que un problema pueda ser resuelto usándoles un algoritmo con esta característica debe tener:

 - El problema debe poder ser descompuesto en subproblemas sequenciales, y cada que uno de estos problemas se resuelve, (es decir, a cada decisión que se toma contribuye al resultado final, reduciendo el tamaño del problema. 

### Ejemplos
Entre los ejemplos que encontrarán sobre problemas que se pueden afrontar con estos algoritmos están:

Problema del agente viajero, los de Minimumm Spanning Tree de Prim y Kruskal, la codificación de Huffman y el problema de la mochila Knapsack.