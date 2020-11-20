---
layout: post
title: Sobrecarga de operadores
date: 2016-12-20 19:00:02
author: Antonio Feregrino
excerpt: Suma, resta, divide, multiplica, compara... todos los tipos de dato que crees para tus aplicaciones, todo gracias a la sobrecarga de operadores que ofrece C&#35;
featured_image: sobrecarga.jpg
images_folder: aprende-c-sharp/
lang: es
tweet_id: 798688388131983360
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Operadores
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Hoy toca hablar de una de las características menos usadas (porque en teoría es mejor no usarlas) de C#. 

Sobrecargar operadores nos ayudará a emplear los operadores comunes como `+`, `-`, `==` para nuestros los tipos de dato que creamos para nuestra aplicación. Pero antes de entrar en el tema, pongámonos un poco festivos y creemos una clase llamada `Toy` que representará un juguete, el cual debe tener un precio y un nombre:

```csharp  
public class Toy
{
    public string Name { get; set; }
    public double Price { get; set; }
    // ...
```  

Creamos unos cuantos juguetes:   

```csharp  
var buzzLightyear = new Toy { Name = "Buzz", Price = 20 };
var woody = new Toy { Name = "Woody", Price = 20 };
var dory = new Toy { Name = "Dory", Price = 15 };
var marlin = new Toy { Name = "Marlin", Price = 15 };
var nemo = new Toy { Name = "Nemo", Price = 15 };
var mikey = new Toy { Name = "Michelangelo", Price = 25 };
var leo = new Toy { Name = "Leonardo", Price = 25 };
var donny = new Toy { Name = "Donatello", Price = 25 };
var raph = new Toy { Name = "Raphael", Price = 25 };
var splinter = new Toy { Name = "Master Splinter", Price = 30 };
```  

## Intro  

Ahora, en la aplicación que estamos desarrollando necesitamos saber si dos juguetes son iguales, o si uno es más caro que el otro. Nos queda claro que siempre podríamos hacer comparaciones como `buzzLightyear.Price > woody.Price` para saber si uno vale más que el otro... pero, ¿y si mejor sobrecargamos el operador `>` para poder comparar los juguetes directamente?

Para sobrecargar un operador es necesario escribir unas líneas de código parecidas a cuando definimos un método público estático en una clase:

```csharp  
public static bool operator <(Toy a, Toy b)
{
    return a.Price < b.Price;
}
```  

Como puedes ver tenemos los modificadores `public` y `static`, junto con el tipo de retorno (que es `bool`) y la palabra clave `operator` seguida del operador que queremos sobrecargar. Este "método" recibe dos parámetros de los cuales uno de ellos debe ser el tipo en el que está definido (en este caso `Toy`).

Por ejemplo, podríamos sobrecargar `<` de esta manera: 

```csharp  
public static bool operator <(Toy a, double price)
```  

Para poder comparar un `Toy` contra un valor de tipo doble: `leo < 10d`, es importante que tomes en cuenta que únicamente puedes comparar "Toy < Double" y no "Double < Toy", para esto deberías sobrecargar nuevamente el operador pero invirtiendo el orden de los parámetros (`<(double price, Toy a)`).

### Operadores sobrecargables  
Los operadores que pueden ser sobrecargados son los siguientes:  

<table>
    <tr>
        <td><b>Operadores</b></td>
        <td><b>Tipo</b></td>
    </tr>
    <tr>
        <td>+, -, !, ~, ++, --, true, false</td>
        <td>Unarios</td>
    </tr>
    <tr>
        <td>+, -, *, /, %, &amp;, |, ^, &lt;&lt;, &gt;&gt;</td>
        <td>Binarios</td>
    </tr>
    <tr>
        <td>==, !=, &lt;, &gt;, &lt;=, &gt;=</td>
        <td>Comparación*</td>
    </tr>
</table>

### *Operadores complementarios
Los operadores de comparación son un caso muy peculiar, ya que es necesario siempre sobrecargarlos en pares: `=` y `!=`, `<` y `>`, `<=` y `>=`. Es decir, en nuestro ejemplo anterior también debemos crear una sobrecarga así: 

```csharp  
public static bool operator >(Toy a, Toy b)
{
    // ...
```  

Ya que de no hacerlo, nuestro programa no compilaría.

En el código de ejemplo que puedes descargar de GitHub están todos los operadores de comparación sobrecargados, gracias a lo cual podemos ejecutar código como el siguiente:

```csharp  
Console.WriteLine(dory.Name + ">" + woody.Name + ": " + (dory > woody));
Console.WriteLine(dory.Name + "<" + woody.Name + ": " + (dory < woody));
Console.WriteLine(buzzLightyear.Name + "==" + woody.Name + " = " + (buzzLightyear == woody));
Console.WriteLine(dory.Name + "==" + woody.Name + ": " + (dory == woody));
Console.WriteLine(splinter.Name + ">=" + woody.Name + ": " + (splinter >= woody));
Console.WriteLine(leo.Name + "<=" + raph.Name + ": " + (leo <= raph));
```  

Que imprimirá: 

<pre>
Dory>Woody: False
Dory<Woody: True
Buzz==Woody = True
Dory==Woody: False
Master Splinter>=Woody: True
Leonardo<=Raphael: True
</pre>

## Más sobrecargas    
Ahora, supón que tu aplicación también debe poder generar paquetes de juguetes, que representaremos con la clase `Bundle` (que <a href="../yield-c-sharp">implementa IEnumerable</a>):  

```csharp  
public class Bundle : IEnumerable<Toy>
{
    public int Size { get; private set; }
    public int ToyCount { get { return _storage.Count; } }
    private List<Toy> _storage;

    public Bundle(int size)
    {
        Size = size;
        _storage = new List<Toy>(Size);
    }

    public bool TryAddToy(Toy t)
    {
        if (ToyCount >= Size) return false;
        _storage.Add(t);
        return true;
    }
    // ...
```  

Como puedes ver, existe un método llamado `TryAddToy` que nos ayudará a agregar un nuevo juguete al paquete, pero... ¿no sería genial que pudiéramos "sumar" juguetes y que de ellos resulte un paquete nuevo?

Es entonces cuando aparece nuevamente la sobrecarga de operadores como una alternativa que nos permite realizar lo que queremos. Los pasos a seguir son muy similares a sobrecargar los otros operadores, con la diferencia de que el tipo de retorno puede ser cualquiera:  

```csharp  
public static Bundle operator +(Toy a, Toy b)
{
    Bundle bundle = new Bundle(2);
    bundle.TryAddToy(a);
    bundle.TryAddToy(b);
    return bundle;
}
```  

Nuevamente `public static`, el tipo de retorno que en este caso es `Bundle`, la palabra `operator` y el operador a sobrecargar seguido de los dos parámetros (de los cuales al menos uno tiene que ser el tipo de dato en el que está definido).

Una vez hecho esto, podemos escribir código como el siguiente:  

```csharp  
Bundle toyStoryBundle = buzzLightyear + woody;
Console.WriteLine(toyStoryBundle);
```  

Y se compilará y ejecutará sin problema, dejando como resultado el siguiente:  

<pre>
Bundle (2/2) [Buzz, Woody]
</pre>

Al igual que con la comparación, también podemos sumar objetos de distinto tipo, siempre prestando atención al orden de los parámetros, de tal manera que podemos definir sumas de este modo dentro de la clase `Bundle`:  

```csharp  
public static Bundle operator +(Bundle bundle, Toy t)
{
    var newBundle = new Bundle(bundle.Size + 1);
    foreach (var toy in bundle)
    {
        newBundle.TryAddToy(toy);
    }
    newBundle.TryAddToy(t);
    return newBundle;
}

public static Bundle operator +(Toy t, Bundle bundle)
{
    if (!bundle.TryAddToy(t))
        throw new InvalidOperationException("Bundle is already full!");
    return bundle;
}
```  

Como te puedes dar cuenta a pesar de que parece que se sobrecarga el mismo operador, los métodos realizan distintas acciones, que podemos probar con el siguiente código:

```csharp  
var findingNemoBundle = dory + marlin + nemo;
```  

El orden de ejecución es el siguiente:  

 1. `dory + marlin` generan un nuevo `Bundle` de tamaño 2
 2. A este nuevo bundle se le suma `nemo`
 3. Obtenemos un nuevo `Bundle` de tamaño 3

Si quisieramos ejecutar algo como esto:  

```csharp  
var findingNemoBundle = dory + (marlin + nemo);
```  

Obtendríamos un nuevo orden de ejecución:  

 1. `marlin + nemo` generan un nuevo `Bundle` de tamaño 2
 2. A este nuevo bundle se le suma `dory`
 3. Obtenemos una excepción ya que esta sobrecarga no permite añadir un juguete si el paquete ya no tiene espacio.

### Operadores de asignación  
Los operadores de asignación (`+=`, `/=`, ...) no son sobrecargables, pero dependein implícitamente de lo que hagas con los operadores binarios (`+`, `/`, ...)
ya que esto:  

```csharp  
tortugasNinja += raph;
```  

Se convierte en esto:  

```csharp  
tortugasNinja = tortugasNinja + raph;
```  

## Un consejo  
Sí la sobrecarga de operadores es fantástica y puede hacer que tu código se vea más limpio y con mayor contexto. Sin embargo, debes tener en cuenta que como todo en esta vida, debes usarlo con medida ya que en caso de abusar podrías hacer que el programa que estás haciendo se vuelva ilegible y poco mantenible.  

Ah, y no olvides que el orden de los parámetros en la sobrecarga importa mucho.  

