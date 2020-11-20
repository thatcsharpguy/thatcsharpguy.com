---
layout: post
title: Enums en C#
date: 2016-08-01 19:00:01
author: Antonio Feregrino
excerpt: Los enum son un tipo de dato que nos permite expresar valores constantes, con la particularidad de poderlos agrupar lógicamente de acuerdo a la relación que tienen unos con otros.
featured_image: enums.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Enums
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Dentro de nuestro código hay ocasiones en las que es necesario contar con un conjunto de valores bien definidos, relacionados y cuyo valor permanezca a lo largo de la ejecución del programa en cuestión. Por poner ejemplos de este tipo de valores, podemos pensar en los siguientes:
  
 - Hacia donde está alineado un texto: derecha, izquierda, centrado o justificado  
 - Los meses del año: enero, febrero, abril, ..., noviembre o diciembre  
 - Sistemas operativos (sé que hay más): Windows, Mac o Linux

Algunos lenguajes resuelven este problema mediante el uso de constantes con algo en común en su nombre, por ejemplo: 

 - `TEXT_ALIGN_RIGHT`, `TEXT_ALIGN_LEFT`, `TEXT_ALIGN_CENTER` o `TEXT_ALIGN_JUSTIFY`  
 - `MONTHS_JANUARY`, `MONTHS_FEBRUARY`, `MONTHS_APRIL`, ..., `MONTHS_NOVEMBER` o `MONTHS_DECEMBER`  
 - `OperatingSystemsWindows`, `OperatingSystemsMac` o `OperatingSystemsWindows`  

### Los Enums

Mientras que C# permite la declaración de valores constantes mediante la palabra `const`, es una buena práctica hacer uso de los `enum`, que es un tipo de dato que nos permite expresar valores constantes, pero con la capacidad de ser agrupados por cómo se relacionan, por ejemplo, las opciones que tenemos para alinear un texto:  

```csharp  
public enum TextAlign
{
    Right,
    Left,
    Center,
    Justify
}
```  

Luego, para poder hacer uso de ellos es necesario hacer referencia al nombre de la enumeración seguido de un punto `.` y el valor que queremos que tome. De este modo se pueden usar en todos lados: como valor de retorno, como parámetros de método y dentro de sentencias de control:

```csharp  
var textAlignment = TextAlign.Center;

// ...

switch (textAlignment)
{
    case TextAlign.Center:
        Console.WriteLine("Texto centrado");
    break;
}
```  

En realidad, los `enum` en C# son una especie de <a href="#">azúcar sintáctica</a> para referirse a números enteros. Por default, el tipo de dato detrás de los `enum` es `int` y el valor inicial de los enums es 0 y aumenta de uno en uno, es por eso que podemos hacer algo como esto:

```csharp  
Console.WriteLine((int)textAlignment); // 2

textAlignment = (TextAlign)1;
Console.WriteLine(textAlignment); // Left
```  

Si por alguna razón necesitamos cambiar el tipo de dato asociado a la enumeración o modificar los valores debajo de cada elemento de la enumeración podemos hacer algo como esto:

```csharp  
public enum Months : short
{
    January = 123,
    February = 431,
    March = 432,
    April = 120,
    // Jupiter = 100000000000000 // 100000000000000 is not a short
}
```  

## Combinando valores de enum
Pero, existen ocasiones en que los valores no son mutuamente exclusivos ¿no?, ¿qué podríamos hacer si por ejemplo, quisieramos saber los días de la semana que una alarma debe funcionar? ¿o qé tal si queremos saber qué sistemas operativos conocen nuestros usuarios?  

Para nuestra fortuna, podemos asignar potencias de dos (1, 2, 4, 6, 16, ...) y el operador *or* (`|`) para combinar los valores de la enumeración, así como el método `HasFlag` para saber si un valor de enumeración contiene un elemento dado.

```csharp  
public enum OperatingSystems
{
    Windows = 1,	// ...000001
    Mac = 2,		// ...000010
    Linux = 4,		// ...000100
    Other = 8		// ...001000
}

// ... 
var pl1 = OperatingSystems.Mac | OperatingSystems.Windows;
Console.WriteLine(pl1); // 3
Console.WriteLine((int)pl1); // 3

if (pl1.HasFlag(OperatingSystems.Mac))
{
    Console.WriteLine("User knows Mac"); // <-- This will execute
}

var pl2 = OperatingSystems.Other | OperatingSystems.Linux | OperatingSystems.Mac;
Console.WriteLine(pl2); // 14
```  

### Flags  
Como puedes ver, queremos mostrar los valores que contiene un `enum` combinado, únicamente obtenemos el entero que resulta de la combinación, ante lo cual podemos hacer uso del <a href="https://msdn.microsoft.com/es-MX/library/aa288454(v=vs.71).aspx" target="_blank" rel="nofollow">atributo</a> `FlagsAttribute` que se coloca sobre la declaración de un tipo y permiten permiten a la enumeración mostrarse de manera correcta:

```csharp  
[Flags]
public enum DaysOfWeek 
{
    Sun = 1, Mon = 2, Tue = 4, 
    Wed = 8, Thu = 16, Fri = 32, 
    Sat = 64
}

// ...

var alarmGoesOffOn = DaysOfWeek.Mon | DaysOfWeek.Wed | DaysOfWeek.Fri;
Console.WriteLine("Alarm goes off on: " + alarmGoesOffOn); // Alarm goes off on: Mon, Wed, Fri
```  

Es importante señalar que por si mismo, el atributo lo, único que hace es permitir que el método `ToString` devuelva una representación "amigable" de la enumeración.  

No olvides descargar el código para que practiques.

### Beneficios de las enumeraciones  
Usar `enum`s en lugar de constantes de tipo entero nos provee el beneficio de dotar de mayor semántica a nuestro código, ya que no es lo mismo tener varias constantes con una ligera similitud en el nombre, a tener un grupo de valores dentro de un tipo `enum`. Sin olvidar que al ser tipos enteros es sencillo su almacenamiento y serialización. 

No olvides <a href=" https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Enums" target="_blank" rel="nofollow">descargar el código</a> para practicar.