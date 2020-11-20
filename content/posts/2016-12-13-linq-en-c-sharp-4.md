---
layout: post
title: Más Linq en C#
date: 2016-12-13 19:00:02
author: Antonio Feregrino
excerpt: Conoce más de linq, métodos para unir, intersectar y filtrar conjuntos.
featured_image: featured.png
images_folder: linq-en-c-sharp/
lang: es
tweet_id: 808854353805242368
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/linq-en-c-sharp-4
workbook: https://raw.githubusercontent.com/ThatCSharpGuy/guoerbucs/master/linq4.workbooko
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Antes de comenzar con el post vamos a declarar algunos métodos y clases que estaremos usando para los ejemplos que veremos más adelante.  

Vamos a necesitar dos métodos utilitarios para imprimir colecciones de datos:

```csharp  
void PrintInline<T>(IEnumerable<T> collection) ...

void Print<T>(IEnumerable<T> collection) ...
```  

Y una clase `Persona` que sobreescriba los métodos `Equals` y `GetHashCode`:

```csharp  
class Persona 
{
    public string Nombre { get; set; }
    public string Apellido { get; set; }
    public override string ToString() { return Apellido + ", " + Nombre; }
    public override bool Equals(object o)
    { 
        var oth = o as Persona;
        if(oth != null)
            return oth.Apellido.Equals(Apellido);
        return false;
    }

    public override int GetHashCode()
    {
        return Apellido.GetHashCode();
    }
}
```  

Listo, más adelante verás por qué sobreescribimos esos dos métodos. Ahora si, comencemos con Linq:

## Zip  
El método Zip nos ayuda para cuando queremos combinar dos colecciones de datos en el orden en el que estan. Con Zip tu especificas la forma en las que se deben combinar, por ejemplo en la siguiente linea se combinan dos series numéricas, multiplicando cada elemento entre si:

```csharp  
var coleccion1 = new int [] { 0, 1, 2, 3, 4, 5 };
var coleccion2 = new int [] { 4, 5, 6, 7, 8, 9 };

var combinados = coleccion1.Zip(coleccion2, (a, b) => a * b);
PrintInline(combinados);
```  

Imprimirá:

```csharp  
0 5 12 21 32 45 
```  

También se pueden combinar cadenas, como las de nombres y apellidos para crear un objeto de la clase Persona:

```csharp  
var nombres = new String[] { "A", "B", "A", "C", "D", "A" };
var apellidos = new String[] { "Z", "X", "Y", "Y", "W", "Z" };

var personas = nombres.Zip(apellidos, (n, a) => new Persona { Nombre= n, Apellido = a}).OrderBy(p => p.Apellido);
var otrasPersonas = nombres.Zip(apellidos.Reverse(), (n, a) => new Persona { Nombre= n, Apellido = a}).OrderBy(p => p.Apellido);
Print(personas);
Print(otrasPersonas);
```  

Imprimirá 

```csharp  
W, D
X, B
Y, A
Y, C
Z, A
Z, A
W, B
X, D
Y, A
Y, C
Z, A
Z, A
```  

## Equals, GetHashCode & IEqualityComparer
En los siguientes métodos vamos a requerir de una forma de discernir si dos instancias de una misma clase son las mismas o no (y es por eso que sobreescribimos `Equals` y `GetHashCode`), sin embargo no siempre tendremos acceso a las "entrañas" de una clase para sobrescribir sus métodos o simplemente queremos tener otra forma de comparar dos objetos. Para estos casos podemos usar una implementación de la interfaz `IEqualityComparer`, por ejemplo, la siguente clase nos ayudará a distinguir una `Persona` de otra únicamente por su nombre:  

```csharp  
class NameEqualityComparer : IEqualityComparer<Persona>
{
    public bool Equals(Persona x, Persona y) => x.Nombre.Equals(y.Nombre);
    public int GetHashCode(Persona obj) => obj.Nombre.GetHashCode();
}
```  

## Union  
Con `Union` se juntan dos colecciones de datos, es importante que se diferencíe este método de Concat, ya que mientras que uno junta las dos colecciones sin preocuparse del contenido de estas, Union no permite que existan duplicados en la colección resultante:

```csharp  
var concatenados = coleccion1.Concat(coleccion2);
var unidos = coleccion1.Union(coleccion2);
PrintInline(concatenados);
PrintInline(unidos);
```  

Imprimirá
```csharp  
0 1 2 3 4 5 4 5 6 7 8 9 
0 1 2 3 4 5 6 7 8 9 
```  

## Intersect  
Es bastante obvio lo que realiza este método, intersecta dos colecciones, dando como resultado otra con solo los elementos que tienen en comun. Nuevamente, usa el método `Equals` (o `IEqualityComparer`)  para decidir qué elementos se parecen y cuales no.  

```csharp  
var interseccion =  coleccion1.Intersect(coleccion2);
PrintInline(interseccion);
```  

Imprimirá únicamente:

```csharp  
4 5 
```  

## Except  
Con `Except` podemos filtrar una colección basándonos en otra. En este caso los únicos elementos que forman parte de la nueva colección que existen en `coleccion1` que no existen en `coleccion2`:

```csharp  
var except = coleccion1.Except(coleccion2);
PrintInline(except);
```  

Imprimirá

```csharp  
0 1 2 3 
```  

## SequenceEquals  
Este método nos ayuda para saber si los elementos que contiene una colección existen en el mismo orden en otra. Si uno tiene menos, si un elemento es distinto o si están en diferente posición, este método devolverá false:

Al igual los demás también este puede funcionar con una implementación de `IEqualityComparer`:

```csharp  
var secuencia1 = Enumerable.Range(0, 4);
var secuencia2 = Enumerable.Range(0, 4);
var secuencia3 = Enumerable.Range(1, 4);

var sonIguales = secuencia1.SequenceEqual(secuencia2);
Console.WriteLine("Son iguales " + sonIguales);

var sonIguales1 = secuencia1.SequenceEqual(secuencia3);
Console.WriteLine("Son iguales " + sonIguales1);
```  

Y obtendremos en pantalla:

```csharp  
Todos divisibles: False
Alguno divisible: True
```  

## Distinct  
Como tal lo dice el nombre, con `Distnict` vamos a poder filtrar los elementos iguales de nuestra colección dejándonos con una que tiene elementos no repetidos entre si:

Este método también acepta una implementación de `IEqualityComparer`:

```csharp  
var distintos = concatenados.Distinct();
PrintInline(distintos);

var personasDistintas = personas.Distinct();
Console.WriteLine("Personas distintas por apellido");
Print(personasDistintas);

var personasDistintasNombre = personas.Distinct(new NameEqualityComparer());
Console.WriteLine("Personas distintas por nombre");
Print(personasDistintasNombre);
```  

Imprimirá:

```csharp  
0 1 2 3 4 5 6 7 8 9 
Personas distintas por apellido
W, D
X, B
Y, A
Z, A
Personas distintas por nombre
W, D
X, B
Y, A
Y, C
```  

## All & Any  
Estos métodos nos ayudan a responder las preguntas: ¿alguno de los elementos cumple con la condición dada? y ¿todos los elementos cumplen con la condición dada?  

```csharp  
bool todosDivisiblesEntre2 = secuencia1.All(i => i % 2 == 0);
bool algunoDivisibleEntre2 =  secuencia1.Any(i => i % 2 == 0);

Console.WriteLine("Todos divisibles: " + todosDivisiblesEntre2);
Console.WriteLine("Alguno divisible: " + algunoDivisibleEntre2);
```  

## Para terminar  
En el siguiente post hablaré de cómo podemos crear lenguajes específicos de dominio usando Linq y métodos de extensión.
