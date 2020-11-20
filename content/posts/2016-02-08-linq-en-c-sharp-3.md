---
layout: post
title: Linq en C# - tercera parte
date: 2016-02-08 20:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Esta es la tercera parte de la serie sobre LINQ en donde hablaré sobre cómo trabajar con dos conjuntos de datos diferentes, con ellos se pueden realizar uniones y agrupaciones para obtener más conocimiento de la información que tenemos.
featured_image: featured.png
images_folder: linq-en-c-sharp/
lang: es
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/linq-en-c-sharp-3
tags: AprendeCSharp, LINQ
featured_tag: AprendeCSharp
---

Inmediatamente después de la [segunda parte de la serie de posts sobre Linq](/post/linq-en-c-sharp-2), les traigo este, que es la tercera parte en donde hablaré sobre cómo trabajar con dos conjuntos de datos diferentes, con ellos se pueden realizar uniones y agrupaciones para obtener más conocimiento de la información que tenemos.
  
### Preparando el terreno  
Nuevamente en este post me auxiliaré de los mismos elementos que en el post pasado: Las clases `Lecture`, `Teacher` y `FakeDatabase`   
   
  
## Agrupamientos (group by , GroupBy)  
Con Linq también podemos realizar agrupamientos de datos, esta función está disponible tanto en la sintaxis de método como en la de consulta y a mi parecer es mucho más entendible en la de consulta. Es importante señalar que cuando ejecutamos un agrupamiento tendremos acceso a una nueva colección de tipo `IGrouping`, y cada uno de esos `IGrouping`s contiene una propiedad `Key` que es del tipo de dato sobre el que ejecutamos el agrupamiento. Podríamos er un ejemplo sencillo si queremos saber cuantas clases tiene cada profesor:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var classesPerTeacherQuery =
    from lecture in db.Lectures
    group lecture by lecture.TeacherId into grp
    select new
    {
        TeacherId = grp.Key,
        LectureCount = grp.Count()
    };
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var classesPerTeacherMethod = db.Lectures
    .GroupBy(lecture => lecture.TeacherId)
    .Select(grp =>
        new
        {
            TeacherId = grp.Key,
            LectureCount = grp.Count()
        });
```  
</div>  
</div>  

En este caso estamos agrupando las clases por maestro, a través de su llave `TeacherId`. Como se puede ver, cuando usamos la sintaxis de consulta es necesario especificar otro nombre para la colección resultante del agrupamiento, en este caso la llamamos `grp`, mientras que en la sintaxis de método no es necesario. Posteriormente podemos seguir trabajando con la colección agrupada, como en este caso que estamos creando un tipo anónimo con el Id del profesor y la cantidad de clases que imparten.  
  
En un ejemplo más complejo podríamos agrupar las clases que da cada maestro y luego agruparlas por nivel para después imprimirlas en pantalla:

```csharp  
var groupedLecturesPerLevelAndTeacher =
    from lecture in db.Lectures
    group lecture by lecture.TeacherId into group1
    select new
    {
        TeacherId = group1.Key,
        Levels = from level in group1
                 group level by level.Level into group2
                 select new
                 {
                    LevelName = group2.Key,
                    Lectures = group2.ToList()
                 }
    };
```  

En este caso se puede decir que estamos haciendo un agrupamiento anidado: Primero estamos agrupando las `Lecture`s por el maestro que las imparte, el resultado de esta agrupación lo estamos colocando en `group1`, después, mediante la cláusula `select` estamos creando un nuevo tipo que tiene como propiedades `TeacherId` y `Level` que es creado a de la agrupación de las clases por la propiedad `Level` (que ponemos en `group2`) para posteriormente crear una colección de objetos con `LevelName` y `Lectures`.

También se pueden hacer agrupamientos un poco más complejas cuando usamos la sintaxis de método, ya que permite especificar una instancia de clase que implementa la interfaz `IEqualityComparer<T>` mediante la cual se implementa el método `Equals(T, T)` que indica cómo se van a agrupar los elementos.  
  
## Uniones (join, Join)  
Una de las características más interesantes de Linq es la de poder hacer uniones entre conjuntos de datos a partir de una propiedad en ellos, la forma de hacer los *join* en Linq es muy similar a la forma en que se realizan los `inner join` en SQL puesto que es necesario especificar un campo sobre el que se realizará la unión:

En las consultas anteriores sobre `db.Lectures` únicamente tenemos acceso a `TeacherId`, pero supongamos que también queremos acceder a la información del profesor asignado a una clase. Es ahí cuando debemos hacer un `join`:   

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var q5 =
    from l in db.Lectures
    group l by l.TeacherId into grouped
    join t in db.Teachers on grouped.Key equals t.Id
    select new
    {
        TeacherName = t.GivenName,
        TeacherId = t.Id,
        LectureCount = grouped.Count()
    };
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var q5alt = db.Lectures
    .GroupBy(l => l.TeacherId)
    .Join(db.Teachers, grouped => grouped.Key, t => t.Id,
    (grouped, t) => new
    {
        TeacherName = t.GivenName,
        TeacherId = t.Id,
        LectureCount = grouped.Count()
    });
```  
</div>  
</div>  

Definitivamente hacer los *join* en la sintaxis de consulta es la mejor opción puesto que se convierten en una instrucción más legible. En el código anterior podemos ver que el *join* se compone de tres palabras:  
 
 - Un alias que va después de la palabra `join`  
 - Un origen de datos que va después de la palabra `in`  
 - Una condición bajo la que se realizará la unión después de la palabra `on`  
  
Si has escrito SQL seguramente te resultará conocida esta sintaxis.  
  
## Operaciones de un elemento (First, Single, ElementAt)  
Como vimos antes, podemos usar `Take` para obtener una cantidad fija de elementos de una secuencia, sin embargo, para obtener uno solo es recomendable usar los métodos como `Single`, `First` o `ElementAt` y sus similares para dicha tarea. Estos métodos no están disponibles a través de la sintaxis de consulta, únicamente a través de la de método.  
  
Por ejemplo, vamos a trabajar con un conjunto de maestros ordenados por su apellido:

```csharp  
var q6 = from l in db.Teachers
            orderby l.LastName descending
            select l;
```  

### First

Si queremos obtener el primer maestro:

```csharp  
var maestro1 = q6.First();
```  

`First` también acepta una condicional, digamos que queremos obtener el primer maestro que tiene como nombre "Cosme Fulanito":

```csharp  
var cosmeFulanito = q6.First(m => m.GivenName == "Cosme" && m.LastName == "Fulanito");
```  

Con este tipo de operaciones debemos ser muy cuidadosos, porque en el ejemplo pasado, al no existir un maestro llamado "Cosme Fulanito", el método `First` lanza una excepción del tipo `InvalidOperationException`, lo cual ocurre si el conjunto sobre el que operamos no contiene elemento alguno que cumpla la condición (o si no indicamos condición basta con que el conjunto esté vacío).  
  
Para evitar la excepción podemos usar el método `FirstOrDefault` el cual regresará el valor por default del elemento del conjunto sobre el que trabajamos, hay que recordar que el valor por default de un tipo por referencia es `null`:

```csharp  
var cosmeFulanito2 = q6.FirstOrDefault(m => m.GivenName == "Cosme" && m.LastName == "Fulanito");
```  

### Single

Adicionalmente a `First`, también existe el método `Single` y `SingleOrDefault` **pero solamente deben ser usados cuando el conjunto sobre el que operan únicamente contiene 1 solo elemento**, ya que de otro modo obtendremos una espantosa excepción, por ejemplo, si ejecutáramos `Single` sobre `q6` obtendríamos una excepción porque tenemos más de 1 maestro en nuestra base de datos falsa:

```csharp  
var maestro3 = q6.Single(); // Excepción porque q6 contiene más de 1 elemento
```  
 
Tal vez te estés preguntando para qué existe `Single` si ya existe `First` y yo al igual que muchos coincido en que todo es cuestión de semántica en nuestro código. Si queremos dejar claro que una consulta a la base de datos, a un XML o a cualquier otra cosa a través de Linq debe siempre regresar un solo resultado debemos usar `Single`, si esperamos obtener el primer resultado de un conjunto más grande debemos usar `First`.  
  
### ElementAt  
No no siempre vamos a necesitar el primer elemento de una colección, tal vez estemos buscando el segundo, el décimo o el noningentésimo... uno nunca sabe. Para realizar esta tarea tenemos a nuestra mano el método `ElementAt` que obtiene el elemento colocado en la posición especificada mediante un entero:  

```csharp  
var maestroSegundo = q6.ElementAt(2);

var maestroDecimo = q6.ElementAt(10);
```  

En este caso también debemos ser cuidadosos ya que, si nuestro conjunto no contiene al menos la cantidad de elementos necesaria para cumplir nuestra petición, obtendremos otra excepción, en este caso una del tipo `ArgumentOutOfRangeException`, así pues que cuando se solicita al maestro 900 obtendremos el error:
 
```csharp  
var maestroNoningentesimo = q6.ElementAt(900); // Excepción ¡porque q6 no tiene 900 maestros!
```  
    
#### Lo que sigue  
Aún quedan algunas cuantas herramientas de Linq por demostrar, entre ellas las intersecciones, uniones y demás operaciones sobre conjuntos que se pueden realizar. Sin duda espero haberte convencido (o demostrado) que el poder de esta característica que nos ofrece .NET es bastante bueno y que no debe pasarse por alto cuando desarrollas. Como siempre, recuerda que todo el código fuente de este post está en la información (cerca del título).