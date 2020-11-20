layout: post
title: Clases genéricas en C#
date: 2016-06-12 19:00:01
author: Antonio Feregrino
excerpt: Los tipos genéricos son un mecanismo de C# que nos ayuda a maximizar la reutilización de código. La reutilización de código se logra a través de el uso de plantillas de código en las cuales se insertan marcadores que representarán los tipos de dato que usaremos en nuestra plantilla.
featured_image: generics-es.png
tweet_id: 747581761232613377
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Generics
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp

Más de C#. En este post toca hablar sobre los genéricos

Los genéricos, y más concretamente en este post los tipos genéricos, son un mecanismo de C# que nos ayuda a maximizar la reutilización de código. La reutilización de código se logra a través de el uso de plantillas (*templates*) de código en las cuales se insertan marcadores (*placeholders*) que representarán los tipos de dato que usaremos en nuestra plantilla.

## Sintaxis

La declaración de tipos genéricos se realiza casi como cualquier otro tipo, con la diferencia de que tenemos que declarar los *placeholders* entre signos `<` y `>`, por ejemplo, 

```csharp  
public class Box<T>
```  

En la cual le estamos indicando que usaremos un marcador llamado `T`. Que se lee como "Box de T". Luego entonces dentro de la declaración de la clase podemos reutilizar el marcador `T` tantas veces como queramos:

```csharp  
    public T Content { get; private set; }

    public  Box(T content)
    {
        Content = content;
    }
```  

Para hacer uso de un tipo genérico la sintaxis es más o menos igual a cualquier tipo por referencia: debemos usar el operador `new`, el constructor de la clase e indicarle dentro de `<` y `>` los tipos con los que queremos que se reemplaze el marcador.

```csharp  
var cajaDeEntero = new Box<int>(5);
var cajaDeString = new Box<string>("Hola mundo");

Console.WriteLine(cajaDeEntero.Content); // 5
Console.WriteLine(cajaDeString.Content); // Hola mundo
```  

También podemos anidar declaraciones en los tipos genéricos:

```csharp  
var cajaDeCajas = new Box<Box<string>>(cajaDeString);
Console.WriteLine(cajaDeCajas); // [Box: Content=[Box: Content=Hola mundo]]
```  

No hay "límite" en cuanto al nombre o cantidad de tipos que podemos usar, por ejemplo:

```csharp  
public class ComplexBox<T, Content1, Content2>
{
    public Content1 C1 { get; set; }
    public Content2 C2 { get; set; }
    public T Item { get; set; }

    public ComplexBox(Content1 c1, Content2 c2)
    {
        C1 = c1;
        C2 = c2;
    }
}

// ...

var cajota = new ComplexBox<double, decimal, float>(1, 2)
{
    Item = 3
};
```  

### Restricción where  

De entrada, si solo especificamos de esta manera `Box<T>` los genéricos, podríamos crear objetos a partir de `Box` sin limitacio

Es por eso que usando la cláusula *where X : [Condición]* podemos limitar qué tipo de datos aceptará nuestro tipo genérico como *marcadores*, por ejemplo, supongamos que tenemos las clases:

```csharp  
public class LimitedBox<T> where T : struct
    // ...

public class LimitedBox<X0,X1> 
    where X0 : struct
    where X1 : IEquatable<X1>
    // ...

public class LimitedBox<T, U, V>
    where T : struct
    where U : IEquatable<U>
    where V : new()
    // ...
```  

Estamos limitando a que:

  - `LimitedBox<T>` únicamente acepte en `T`, tipos que sean `struct` como `decimal` y `DateTime`
  - `LimitedBox<X0, X1>` únicamente acepte en `X0`, tipos que sean `struct` como `decimal` y `DateTime` y en `X1` objetos que implementen la interfaz `IEquatable`
  - `LimitedBox<T, U, V>` similar al anterior, solo que en el tercer parámetro, `V`, únicamente aceptará objetos que tengan un constructor vacío que se a público

La lista completa de restricciones se encuentra en <a href="https://msdn.microsoft.com/en-us/library/d5x73970.aspx" target="_blank" rel="nofollow">este enlace</a>.

### Ejemplos  

Pero bueno, veamos un ejemplo. 

Imaginate un programa para gestionar un deportivo, en el que existen equipos de futbol y de béisbol, compuestos por atletas, `Athlete`, (que a su vez se divide en futbolistas, `FootballPlayer`, y por beisbolistas, `BaseballPlayer`):

```csharp  
public class Athlete
{
    public string Name { get; set; }
    public double Height { get; set; }
    public double Weight { get; set; }
}
```  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class FootballPlayer : Athlete
{
    public string Position { get; set; }
    public int Goals { get; set; } 
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class BaseballPlayer : Athlete
{
    public string Position { get; set; }
    public int GamesStarted { get; set; }  
}
```  
</div>  
</div>

Ahora, para crear los equipos podríamos tener clases separadas para representar cada equipo:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class BaseballTeam
{
    public string CoachName { get; set; }
    public BaseballPlayer[] Members { get; set; }

    public BaseballTeam(int maxMembers)
    {
        Members = new BaseballPlayer[maxMembers];
    }
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class FootballTeam
{
    public string CoachName { get; set; }
    public FootballPlayer[] Members { get; set; }

    public FootballTeam(int maxMembers)
    {
        Members = new FootballPlayer[maxMembers];
    }
}
```  
</div>  
</div>

Y para crear equipos tendríamos que hacer algo así:

```csharp  
var vitesse = new FootballTeam(18); 
vitesse.Members[0] = new FootballPlayer { Name = "Alex Renato Ibarra Mina" };
        
var losAngeles = new BaseballTeam(25);
losAngeles.Members[0] = new BaseballPlayer { Name = "Julio César Urías" };
```  

### Genéricos  

O, para maximizar la reutilización de código, podríamos crear una clase genérica, digamos `Team<T>` ("Team de T"), para poder reutilizarla (y no solo con equipos de futbol o beisbol):

```csharp  
public class Team<T>
{
    public string CoachName { get; set; }
    public T[] Members { get; set; }

    public Team(int maxMembers)
    {
        Members = new T[maxMembers];
    }
}
```  

Y usarlo de esta manera:

```csharp  
var vitesse = new Team<FootballPlayer>(18);
vitesse.Members[0] = new FootballPlayer { Name = "Alex Renato Ibarra Mina" };

var losAngeles = new Team<BaseballPlayer>(25);
losAngeles.Members[0] = new BaseballPlayer { Name = "Julio César Urías" };
```  

Si dejamos la clase `Team<T>` como está, podríamos hacer cosas como esta:  

```csharp  
var equipoDeEnteros = new Team<int>(10);
var otroEquipo = new Team<object>(1233);
```  

Que tal vez no tienen mucho sentido en nuestra aplicación. Es por eso que usando la restricción `where` podemos limitar al tipo `Team<T>` para que únicamente acepte `Athlete`s:

```csharp  
public class Team<T> where T : Athlete
```  

## Ejemplos del Framework

En el Framework .NET existen numerosos ejemplos de tipos genéricos, siendo el más usado y conocido, las <a href="https://msdn.microsoft.com/es-es/library/bb762916(v=vs.110).aspx" target="_blank" rel="nofollow">colecciones genéricas</a> y las <a href="../tuples-c-sharp">tuplas en C#</a>.

## Para cerrar

Usar los genéricos permiten dos cosas: Incrementar la reutilización de código manteniendo el tipado fuerte y reducir el impacto al desempeño en nuestras apps al evitar el <a href="https://msdn.microsoft.com/en-us/library/yz2be5wk.aspx" target="_blank" rel="nofollow">boxing y unboxing</a>, de ahí la importancia de su existencia.

Además, los genéricos existen también para métodos, post sobre el que estaré escribiendo en el futuro.