layout: post
title: Inicializadores en C#
date: 2016-03-14 19:00:00
author: Antonio Feregrino
excerpt: Lo que permite la inicialización de objetos es hacer uso de los constructores y además de asignar &quot;al mismo tiempo&quot; las propiedades o campos públicos que contiene el objeto.
featured_image: inicializadores.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/inicializadores
lang: es
tweet_id: 709549005865242624
tags: AprendeCSharp
featured_tag: AprendeCSharp

## Inicializador de objetos  
Seguramente ya conocerás que en C# para crear una instancia de un objeto es necesario utilizar alguno de los constructores de la clase que lo define. Y mientras que es te comportamiento es algo estándar para todos los lenguajes de programación orientados a objetos, C# permite hacer uso de  una sintaxis conocida como "Inicializadores de objeto", que fue introducida en la versión 3.0 del lenguaje.

Para comenzar, a explicar los inicializadores, fíjate en la definición de la siguiente clase:

```csharp  
class Person
{
    public Person(string name)
    {
        Name = name;
    }
    
    public Person(string name, Person dad, Person mom)
    {
        Name = name;
        Dad = dad;
        Mom = mom;
    }
    
    public string Name { get; private set; }
    public int Age { get; set; }
    public Person Dad { get; set; }
    public Person Mom { get; set; }
    public List<string> Abilities { get; set; }
}
```  

Podemos ver que para crear una persona, es necesario llamar al constructor pasándole un nombre, y dos instancias de esa misma clase, por ejemplo:

```csharp  
var dad = new Person("Roberto");
var mom = new Person("Carla");
```  

Para asignar las propiedades `Age` y `Abilities` son asignadas en instrucciones por separado y una vez que el objeto ha sido creado:

```csharp  
mom.Age = 55;
mom.Abilities = new List<string>();
```  

Hasta aquí, todo normal, pero lo que permite la inicialización de objetos es hacer uso de los constructores y además de asignar "al mismo tiempo" las propiedades o campos publicos que contiene el objeto, estén o no definidos en el constructor usado. Esto significa que para la clase `Person` podemos asignar `Age` y `Abilities` dentro de la misma instrucción:

```csharp  
var son = new Person("Omar", dad, mom) 
{ 
    Age = 21, 
    Abilities = new List<string>(), 
};
```  

Las asignaciones de cada propiedad deben estar separadas con una coma y pueden ser asignadas en la misma o diferentes líneas.

En este caso, estamos empleando el constructor de la clase seguido por la asignación de propiedades dentro de las llaves `{` `}`, pero haciéndolo en una sola instrucción, en lugar de usar tres instrucciones separadas, como se haría tradicionalmente:

```csharp  
var son = new Person("Omar", dad, mom);
son.Age = 21;
son.Abilities = new List<string>();
```  

**El comportamiento no se modifica, usando el inicializador de objetos primero es construido el objeto** (usando el constructor) **y después son asignadas las propiedades** en el orden en el que están escritas dentro de las llaves.

### Anidación de inicializadores  
Es posible anidar inicializadores, pudiendo escribir código cómo este:

```csharp  
var aunt = new Person("Claudia")
{
    Age = 50,
    Mom = new Person("Gil")
    {
        Age = 78,
        Abilities = new List<string>(),
        Dad = new Person("Charlie")
        {
            Age = 106,
        }
    }
};
```  

### Inicializadores y tipos anónimos
Como <a href="http://thatcsharpguy.com/post/anonimos-en-c-sharp/">anteriormente escribí</a>, el inicializador de objetos cobra mucha importancia cuando trabajamos con tipos anónimos, y es que esta es la única manera de trabajar con ellos:

```csharp  
var album = new { Title = "Kindred", Artist = "Passion Pit" };
var gift = new { Item = album, To = aunt };
```  

## Inicialización de colecciones  
Al igual que con los objetos, también existe un inicializador para colecciones, la cual permite crear e insertar valores en un arreglo, lista o cualquier otra colección. Por ejemplo, si creamos una lista de cadenas, y luego añadimos cadenas en ella, normalmente el código se vería así:

```csharp  
List<string> abilities = new List<string>();
abilities.Add("programación");
abilities.Add("futbol");
abilities.Add("dormir");
```  

Sin embargo, usando el inicializador de colecciones podemos escribir lo mismo utilizando un inicializador:

```csharp  
List<string> abilities = new List<string> { "programación", "futbol", "dormir", };
```  

Si, por ejemplo, estamos trabajando con un arreglo simple, podemos omitir el uso del operador `new`, y hacer esto:

```csharp  
string[] abilities = { "programación", "futbol", "dormir" };
```  

Además, <a href="http://thatcsharpguy.com/post/arreglos-en-c-sharp-parte-1/">como ya lo había explicado</a> en los <a href="http://thatcsharpguy.com/post/arreglos-en-c-sharp-parte-2/">posts sobre arreglos</a>, se pueden programar cosas como esta:

```csharp  
int [,] oddArray =  { { 1, 2 }, { 3, 4 }, { 5, 6 } };
```  

Es importante señalar que entre cada elemento de nuestra colección debe existir una `,` que las separe.

## Combinando inicializadores  
Podemos usar esta sintaxis siempre que la creamos conveniente, por ejemplo, si deseamos crear un objeto de nuestra clase Person con habilidades establecidas desde la instanciación de la clase, podemos hacer esto:

```csharp  
var daughter = new Person("Lucille", dad, mom)
{
    Age = 10,
    Abilities = new List<string>
    {
        "programación",
        "pintura"
    }
}
```  

O, si por otro lado, queremos crear una colección de personas:

```csharp  
var classAttendance = new List<Person>
{
    new Person("Rafa") { Age = 10},
    new Person("Rosalinda") { Age = 9},
    new Person("Javi") { Age = 10 },
    new Person("Dalia") { Age = 11 },
};
```  

### Un último detalle
Si eres muy fijado, te habrás dado cuenta que hay veces en que después del último elemento o asignación en un inicializador existe una coma `,`, como en el último fragmento de código visto (`new Person("Dalia") { Age = 11 },`). Podrías pensar que es un error de sintaxis... pero no es así, y es que debido a que este es un "error" que puede ser cometido frecuentemente por los programadores cuando se borra el último elemento en una lista o un objeto, se decidió permitir que el último elemento esté seguido por la coma.