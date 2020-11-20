---
layout: post
title: params en C#
date: 2016-04-18 19:00:00
author: Antonio Feregrino
summary: La palabra reservada params se usa en la declaración de los parámetros de un método, se antepone al tipo de dato que vamos a declarar y tiene una peculiaridad, solamente se puede anteponer a parámetros que sean arreglos.
featured_image: params.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/params
tweet_id: 719643375478054913
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---


Justamente la semana pasada escribí sobre cuál es la sintaxis de <a href="../metodos-c-sharp">los métodos en C#</a>, y ese fue más bien un post introductorio y es por eso que necesita ser ampliado. Esta vez toca hablar de la palabra `params` y cómo nos puede facilitar a la hora de escribir el código si es que vamos a trabajar con arreglos.

### params  
La palabra reservada `params` se usa en la declaración de los parámetros de un método, se antepone al tipo de dato que vamos a declarar y tiene una peculiaridad, solamente se puede anteponer a parámetros que sean arreglos, por ejemplo a `string[]`, `Persona[]`.

Esto es porque lo que nos ayuda a escribir es un método que recibe una cantidad variable de argumentos del mismo tipo y los "introduce" un un arreglo sin tener que declararlo explícitamente.

Mira los ejemplos para que te quede un poco más claro:

### Ejemplos

#### Como único parámetro

Para comenzar, tomemos los siguientes métodos:
```csharp  
public static void Metodo1Params(params bool [] a)
{
        Console.WriteLine("M1Params recibió " + a.Length + " booleanos");
}

public static void Metodo1Arrays(bool [] a)
{
    if (a == null)
    {
        a = new bool[]{ };
    }
    Console.WriteLine("M1Arrays recibió " + a.Length + " booleanos");
}
```  

Los dos realizan exactamente lo mismo, sin embargo, para ejecutar cada uno, requerimos de una sintaxis un poco distinta:

```csharp  
Metodo1Arrays(null); // Imprime: M1Arrays recibió 0 booleanos
Metodo1Params(); // Imprime: M1Params recibió 0 booleanos
```  

Podemos pasar variables:

```csharp  
bool cierto = true;
// Metodo1Arrays(cierto); // Nope, necesita un arreglo
Metodo1Arrays(new bool[] { cierto }); // Imprime: M1Arrays recibió 1 booleanos
Metodo1Params(cierto); // Imprime: M1Params recibió 1 booleanos
```  

Como puedes ver, no podemos llamar `Metodo1Arrays` indicando únicamente el argumento que queremos, nosotros tenemos que crear el arreglo para llamarlo, en el siguiente ejemplo está un poco más claro:

```csharp  
Metodo1Arrays(new []{ cierto, false, true, cierto }); // M1Arrays recibió 4 booleanos
Metodo1Params(cierto, false, true, cierto); // M1Params recibió 4 booleanos
```  

#### En conjunto con otros

Podemos declarar un método con varios parámetros, y usar `params` dentro de ellos, siempre y cuando el que marquemos sea el último parámetro de la lista.

```csharp  
public static void Metodo2Params(int a, char b, params decimal[] c)
{
    Console.WriteLine("Metodo3Params: " + c.Length);
}

public static void Metodo2Arrays(int a, char b, decimal[] c)
{
    Console.WriteLine("Metodo3Array: " + c.Length);
}
```  

El hecho de que hayamos marcado algo con `params` no quiere decir que tengamos que no podamos simplemente enviar un arreglo en lugar de una lista de valores separados por coma:

```csharp  
var array = new decimal[] {1,2,3};
Metodo2Arrays(1, 'A', array);
Metodo2Params(1, 'B', array);
```  

La diferencia en la cantidad de código que tenemos que escribir se hace más notoria cuando tenemos que mandar cero elementos:

```csharp  
Metodo2Arrays(1, 'C', new decimal []{ });
Metodo2Params(1, 'D');
```  

Y también cuando vamos a mandar varios:

```csharp  
Metodo2Arrays(1, 'E', new []{ 2.1m, 13m, 15m });
Metodo2Params(1, 'E', 2.1m, 13m, 15m);
```  

#### Arreglos multidimensionales

Y también podemos crear cosas más complejas, como usar arreglos multidimensionales como parámetros:

```csharp  
public static void PromediaCalificacionesArray(decimal [][] calificaciones)
{
    for(int i = 0; i < calificaciones.Length;i++)
    {
        // Mejor usemos LINQ:
        decimal promedio = calificaciones[i].Average();
        Console.WriteLine("(Arrays) Calificación alumno " + i + ": " + promedio);
    }
}

public static void PromediaCalificacionesParams(params decimal [][] calificaciones)
{
    for(int i = 0; i < calificaciones.Length;i++)
    {
        decimal promedio = calificaciones[i].Average();
        Console.WriteLine("(Params) Calificación alumno " + i + ": " + promedio);
    }
}
```  

Y las llamadas al método:

```csharp  
decimal[] alumno1 = new decimal [] { 10m, 9.2m, 7m, 3.5m, 10m };
var alumno2 = new []{ 8.5m, 9.3m, 8.6m, 9.9m, 10m };

PromediaCalificacionesArray(new []{ alumno1, alumno2 });
PromediaCalificacionesParams(alumno1, alumno2);
```  

### Ejemplo de la vida real
Uno de los máximos exponentes del uso de `params` es el método estático `String.Format`, que *formatea* una cadena y a la cual le podemos pasar una lista de argumentos. La firma de dicho método es:

```csharp  
public static string Format(
	string format,
	params object[] args
)
```  

### Ejemplos de lo que no se puede hacer

No podemos marcar un tipo de dato que no sea un arreglo con params, como es el caso de `char a` en el siguiente fragmento:

```csharp  
public static void Metodo1Params(params char a) // Invalido, debe ser un arreglo
```  

Un parámetro marcado con `params` debe ser siempre el último parámetro del método, esto para evitar confusiones a la hora de ejecutarlo:

```csharp  
public static void Metodo3Params(int a, params decimal[] b, char c)
{
    Console.WriteLine("Metodo3Params: " + b.Length);
}
```  

Tampoco podemos declarar dos métodos con el mismo nombre y los mismos parámetros, ya que técnicamente la firma del método es la misma.

```csharp  
public static void Metodo4(params decimal[] b) //...

public static void Metodo4(decimal[] b) //...
```  

### Lo que sucede internamente

Cuando marcamos un parámetro como params, el compilador se encarga de tratarlo internamente como un arreglo: convierte nuestra lista variable de argumentos en un arreglo o crea un arreglo vacío si no enviamos nada. Así que esta palabra reservada es parte de la "azúcar sintáctica" que nos ofrece C#.


### Lo que sigue
Puede que características como la que se describe en este post no tengan mucho (o a veces nada) de impacto en el desempeño de un programa al momento de ejecutarse, sin embargo, no por eso debemos descartarlas como una poderosa herramienta de programación que puede ayudar a darle más significado al código. Como todo, también debes usarlo con moderación y en casos en los que tenga sentido.