---
layout: post
title: La palabra yield en C#
date: 2016-11-22 19:00:01
author: Antonio Feregrino
summary: La palabra yield en C# tiene un uso bastante peculiar pero sencillo de entender cuando se explica de la manera adecuada, básicamente te ayudará a mejorar la eficiencia de tus aplicaciones. En este post trato de explicar qué es y cómo funciona eso a través de ejemplos de código.
featured_image: featured.jpg
images_folder: /aprende-c-sharp/yield/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/yield
lang: es
tweet_id: 801225227430600705
tags: AprendeCSharp
featured_tag: AprendeCSharp
---


## La interfaz IEnumerable
Que un objeto implemente la clase IEnumerable significa que las instancias de esta tienen contenido sobre el que se puede iterar o recorrer. Este recorrido es siempre hacia "adelante". Dicho "en código", la interfaz `IEnumerable` (genérica o no) nos indica que podemos usar un bloque `foreach` para recorrer el objeto que la implemente. Por ejemplo, la clase `List<T>`, `ObservableCollection<T>` y los arreglos implementan esta clase. Es importante mencionar esto, ya que el tema que se verá a continuación está totalmente ligado a esta interfaz.

En el siguiente post vamos a ver una serie de métodos que tienen como tipo de retorno la interfaz `IEnumerable`, que como ya sabes ahora, es un tipo de dato que se puede recorrer, o que se puede iterar sobre él, ya sea en un ciclo `foreach` o empleando Linq.

## ¿Para qué sirve yield?  
Considero que la mejor manera de explicar el uso de esta palabra reservada es mediante un ejemplo, así que observa el siguiente código antes de nada.  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
IEnumerable<int> GetMultiplesOf(int n, 
                                int start, 
                                int end)
{
    List<int> multiples = new List<int>();
    for (int i = start; i < end; i++)
    {
        if (i % n == 0)
        {
            Console.WriteLine(
                $"{i} GetMultiplesOf");
            multiples.Add(i);
        }
    }
    return multiples;
}
```  
</div> 
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
IEnumerable<int> YieldMultiplesOf(int n, 
                                  int start, 
                                  int end)
{  

    for (int i = start; i < end; i++)
    {
        if (i % n == 0)
        {
            Console.WriteLine(
                $"{i} YieldMultiplesOf");
            yield return i;
        }
    }  

}
```  
</div>
</div> 

** Para que dentro de un método se pueda usar la palabra `yield`, este debe tener como tipo de retorno una de las dos variantes de `IEnumerable`.**  

A simple vista pareciera que hacen lo mismo: encontrar los números divisibles entre `n`. La diferencia entre ellos radica en que mientras que uno crea una secuencia intermedia y almacena todos los valores antes de devolverlos, el otro devuelve uno por uno a quién lo llamó.

Para que veas el funcionamiento, vamos a llamarlos dentro de un ciclo `foreach`:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
Console.WriteLine("Normal collection");
foreach (var m in GetMultiplesOf(2, 1, 10))
{
    Console.WriteLine($"{m} es multiplo de 2");
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
Console.WriteLine("Yield collection");
foreach (var m in YieldMultiplesOf(2, 1, 10))
{
    Console.WriteLine($"{m} es multiplo de 2");
}
```  
</div> 
</div>

El resultado de ejecutarlas es el siguiente:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<pre>
Normal collection
2 GetMultiplesOf
4 GetMultiplesOf
6 GetMultiplesOf
8 GetMultiplesOf
2 es multiplo de 2
4 es multiplo de 2
6 es multiplo de 2
8 es multiplo de 2
</pre>
</div>
<div class="pure-u-1 pure-u-md-1-2">
<pre>
Yield collection
2 YieldMultiplesOf
2 es multiplo de 2
4 YieldMultiplesOf
4 es multiplo de 2
6 YieldMultiplesOf
6 es multiplo de 2
8 YieldMultiplesOf
8 es multiplo de 2
</pre>
</div> 
</div>  

Wow, wow, ¿viste lo que ocurrió? mientras que en el que no usa `yield` se calcularon todos los valores en una sola llamada y se almacenaron en una lista temporal, en el otro solamente se calculó uno por uno y solamente cuando fue requerido. Evitándonos así tener que crear colecciones auxiliares para almacenar los datos.  

Para comprender un poco mejor el funcionamiento, mira estas imágenes:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/aprende-c-sharp__yield__get.png" title="Normal Enumerable" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/aprende-c-sharp__yield__yield.png" title="Yield Enumerable" />
</div>
</div>

En resumidas palabras, `yield` nos ayuda a implementar una especie de *"lazy loading"* en nuestro código introduciendo saltos entre un método y quien lo llamó para evitar así desperdiciar memoria en colecciones intermedias. Eso sin mencionar que usando esta técnica también podemos asegurar que para una determinada operación sobre un `IEnumerable` siempre se ejecutará la menor cantidad de iteraciones posibles.

Mira el siguiente ejemplo, en donde se aplican algunas operaciones de Linq:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var get = GetMultiplesOf(2, 320, 335)
            .Skip(3)
            .First();
Console.WriteLine($"El 4to multiplo es {get}");
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var yield = YieldMultiplesOf(2, 320, 335)
                .Skip(3)
                .First();
Console.WriteLine($"El 4to multiplo es {yield}");
```  
</div>
</div>

Y su resultado es el siguiente. Debes notar que para el caso de la colección intermedia se calcularon todos los valores posibles, mientras que para cuando se usó `yield` solo se calcularon los valores necesaros:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<pre>
320 GetMultiplesOf
322 GetMultiplesOf
324 GetMultiplesOf
326 GetMultiplesOf
328 GetMultiplesOf
330 GetMultiplesOf
332 GetMultiplesOf
334 GetMultiplesOf
El 4to multiplo es 326
</pre>
</div>
<div class="pure-u-1 pure-u-md-1-2">
<pre>
320 YieldMultiplesOf
322 YieldMultiplesOf
324 YieldMultiplesOf
326 YieldMultiplesOf
El 4to multiplo es 326




</pre>
</div> 
</div>  

## Multiples returns

Al igual que cuando usamos `return`, un método puede contener varias instrucciones `yield return`. Por ejemplo, imagina un programa en donde tengas que calcular los factores primos de un número:

```csharp  
public static IEnumerable<int> PrimeFactors(int n)
{
    while (n % 2 == 0)
    {
        yield return 2;
        n = n / 2;
    }

    for (int i = 3; i <= Math.Sqrt(n); i += 2)
    {
        while (n % i == 0)
        {
            yield return i;
            n = n / i;
        }
    }

    if (n > 2)
        yield return n;
}
```  


**Por otro lado, si en algun momento queremos terminar la ejecución de este método podemos usar la combinación de palabras `yield break` para hacerlo.**

Sin duda también nos ayuda a implementar la "lazy evaluation" y ejecutar el código solamente cuando lo necesitamos, con ella nos ahorramos el crear una colección intermedia entre la fuente que generará los resultados y el destino de estos.


## Funcionamiento interno   
La palabra `yield` opera insertando saltos entre el método llamado y el código que lo llama... como vimos en una imagen anterior. Pero para que esto funcione, se debe almacenar cierta información que le indique al método llamado  en qué estado se encontraba antes de volver a ser ejecutado. Es por eso que cuando usamos la palabra `yield` el compilador entra en acción para nosotros, crea una clase intermedia en donde se almacena la información del estado de la iteración siguiente, te recomiendo que visites este enlace si deseas saber más.

## Ejemplos de uso  
Los ejemplos mostrados en el código pueden parecer un poco absurdos (lo sé) pero por ejemplo, ¿qué sucedería si en lugar de buscar los factores primos de un número, tuviéramos que encontrar una persona en un repositorio de datos de 1,000,000 de registros? o, ¿qué ocurriría con la memoria de nuestra computadora si tuviéramos que trabajar con las líneas de un archivo de 1Gb?
