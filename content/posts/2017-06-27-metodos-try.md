---
layout: post
title: Métodos Try...
date: 2017-06-27 19:00:01
author: Antonio Feregrino
summary: Las excepciones son eso. Excepciones, así que debes procurar evitarlas en tu código; los métodos Try... te ayudan a hacerlo, conócelos aquí.
featured_image: try.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/MetodosTry
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

En un post pasado <a href="..\excepciones-c-sharp" target="_blank">hablamos sobre las excepciones</a> en C#, y cómo es que pueden ayudarnos a encontrar errores que ocurren en tiempo de ejecución en nuestras aplicaciones.  

Uno de los lugares en donde las excepciones están a la orden del día es cuando estamos tratando de realizar alguna conversión de cadena a un tipo numérico y más aún si vamos a recibir la cadena de un usuario a través de nuestra aplicación.

Por poner un ejemplo, imagina que quieres *convertir* un `string` a un `int`. Normalmente usarías el método `Parse` de la clase `int`:

```csharp  
var valor = int.Parse("0");
```  

Pero si revisas la <a href="https://msdn.microsoft.com/es-es/library/b3h1hf19(v=vs.110).aspx#Anchor_1" target="_blank">documentación</a> del método, verás que este puede lanzar 3 excepciones, entonces, para preparar nuestro programa, debemos manejarlas de alguna manera.

Para este post considera el siguiente arreglo de cadenas: 

```csharp  
var values = new[]
{
    "1",
    "-",
    null,
    " ",
    "2147483648",
    "2147483647",
    "0.1"
};
```  

Y para intentar procesarlas emplearemos un ciclo `foreach`, usando `Parse` para tratar de convertir y capturando las excepciones según sea el caso:

```csharp  
foreach (var value in values)
{
    var intValue = -1;
    try
    {
        intValue = int.Parse(value);
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue);
    }
    catch (FormatException)
    {
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue + " (Error de formato)");
    }
    catch (ArgumentNullException)
    {
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue + " (El valor es null)");
    }
    catch (OverflowException)
    {
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue +
                            " (El valor es más grande que un entero)");
    }
}
```  

El resultado de ejecutar el código anterior es el siguiente:  

<pre>
Cadena "1" resultado: 1
Cadena "-" resultado: -1 (Error de formato)
Cadena "" resultado: -1 (El valor es null)
Cadena " " resultado: -1 (Error de formato)
Cadena "2147483648" resultado: -1 (El valor es más grande que un entero)
Cadena "2147483647" resultado: 2147483647
Cadena "0.1" resultado: -1 (Error de formato)
</pre>

Sin embargo, habrá ocasiones en que no nos interese saber qué excepción ocurrió, entonces podrías usar un `catch` sin especificar la excepción:

```csharp  
foreach (var value in values)
{
    var intValue = -1;
    try
    {
        intValue = int.Parse(value);
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue);
    }
    catch
    {
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue + " (Ocurrió un error)");
    }
}
```  

El resultado de ejecutar el código es este:  

<pre>
Cadena "1" resultado: 1
Cadena "-" resultado: -1 (Ocurrió un error)
Cadena "" resultado: -1 (Ocurrió un error)
Cadena " " resultado: -1 (Ocurrió un error)
Cadena "2147483648" resultado: -1 (Ocurrió un error)
Cadena "2147483647" resultado: 2147483647
Cadena "0.1" resultado: -1 (Ocurrió un error)
</pre>

## TryCatch  
Pero, en realidad no es necesario que captures una excepción en el caso de `Parse`, hay muchas clases que ofrecen una opción "segura" de convertir un tipo de dato en otro. Para esto podemos usar los métodos `Try...`, como `TryParse` en el caso de `int`:

```csharp  
foreach (var value in values)
{
    int intValue;
    if (int.TryParse(value, out intValue))
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue);
    else
        Console.WriteLine("Cadena \"" + value + "\" resultado: " + intValue + " (Ocurrió un error)");
}
```  

El resultado es el siguiente:

<pre>
Cadena "1" resultado: 1
Cadena "-" resultado: 0 (Ocurrió un error)
Cadena "" resultado: 0 (Ocurrió un error)
Cadena " " resultado: 0 (Ocurrió un error)
Cadena "2147483648" resultado: 0 (Ocurrió un error)
Cadena "2147483647" resultado: 2147483647
Cadena "0.1" resultado: 0 (Ocurrió un error)
</pre>

La "magia" de este recae en el segundo parámetro <a href="..\out-ref-c-sharp" target="_blank">marcado como out</a>, ya que dentro del método se intenta realizar una conversión y si no es posible no lanza una excepción, sino que no pasa nada, solamente le asigna el valor por default al entero (el valor por default es 0).

La forma de saber que un método `TryParse` falló es porque su valor de retorno es `false`. El método regresará `true` siempre que la conversión sea exitosa. Lo que hace atractivo a esta clase de métodos es que ni dentro de ellos se lanza excepción alguna, estos utilizan otros mecanismos para *parsear* de cadena al tipo de dato.

## Beneficios  
Además de la obvia cantidad de código que podemos evitar escribir al no tener que escribir el manejador de cada excepción, también podemos evitarnos el ligero incremento en el costo de procesamiento que implica lanzar una excepción. Este costo se debe a la cantidad de información que se adjunta a cada excepción para hacerla más útil a la hora de rastrear su origen.

## Otros métodos `Try`
Los tipos nativos (`int`, `decimal`, `bool`...) no son los únicos que tienen este tipo de métodos, también los podemos encontrar para cuando vamos a sacar un valor de un `Dictionary` y dentro de las colecciones concurrentes como `ConcurrentStack`, `ConcurrentQueue`. Y sin lugar a dudas, si tu creas agún tipo con métodos que puedan lanzar una excepción, tal vez sería buena idea también crear una versión de dicho método que emule el comportamiento de los `Try...`

## ¿Deberías usarlos?
Sí, siempre que no tengas una razón muy grande para usar las excepciones. Una razón muy grande para usar las excepciones es que debas saber por qué determinada acción no se pudo realizar. Por ejemplo, en el caso de los enteros, que requieras saber si el número era más grande que un `int` o si no venía en el formato adecuado. En cualquier otro escenario deberías aprovechar la rapidez de `TryParse`.  

Además recuerda, las excepciones son para precisamente eso: caso excepcionales en durante la ejecución de un programa.