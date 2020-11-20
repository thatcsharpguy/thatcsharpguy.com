---
layout: post
title: Linq en C# - segunda parte
date: 2016-02-01 20:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Conoce un poco más sobre el poder de Linq a través de ejemplos sencillos. Promedios, sumas, filtrados, ordenamientos y más.
featured_image: featured.png
images_folder: linq-en-c-sharp/
lang: es
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/linq-en-c-sharp-2
tags: AprendeCSharp, LINQ
featured_tag: AprendeCSharp
---

Hace algunos cuantos posts [les hablaba de Linq en C#](/post/linq-en-c-sharp), y este post es una especie de continuación, para que, si aún no has integrado esta poderosa herramienta dentro de tus tareas de desarrollo, por fin te animes a hacerlo. 
  
### Preparando el terreno  
Para este post me auxiliaré de tres elementos.  

 - Las clases `Lecture`, `Teacher` que representarán nuestros objetos de negocios. Un profesor (`Teacher`) imparte una o más clases (`Lecture`).  
 - `FakeDatabase` que como su nombre lo indica, pretende ser una simulación de un almacenaje de datos con soporte para Linq. Hay que recordar que existen varias implementaciones: Linq to objects, Linq to SQL, Linq to XML, ...  
   
## Dos sintaxis  
Para usar Linq, tenemos disponibles dos formas de hacerlo: mediante el uso de la sintaxis de método o mediante la sintaxis de consulta, ambas son en su mayor parte intercambiables, sin embargo hay algunas funciones que únicamente son accesibles mediante la sintaxis de método. Diferenciarlas es sencillo, una son los métodos tradicionales mientras que la otra hace uso de palabras especiales como `from`, `in`, `select`.  

Por ejemplo, estas dos operaciones con Linq hacen lo mismo:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
from lecture in db.Lectures
group lecture by lecture.TeacherId into grouped
join teacher in db.Teachers 
    on grouped.Key equals teacher.Id
where teacher.LastName == "Williams"
select new { TeacherId = grouped.Key, 
             Name = teacher.GivenName };
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
db.Lectures
.GroupBy (lecture => lecture.TeacherId)
.Join (db.Teachers,
        g => g.Key, t => t.Id,
        (g, t) => new {Grouped = g, Teacher = t})
.Where (an => an.Teacher.LastName == "Williams")
.Select (an => new { TeacherId = an.Grouped.Key,
                     Name = an.Teacher.GivenName });
```  
</div>
</div>  
  
Tal vez con solo ver los ejemplos anteriores ya tengas tu favorito, pero no te dejes llevar todavía, los dos tienen sus fortalezas. Sin olvidar que hay operaciones únicamente disponibles en la sintaxis de método, para mí la sintaxis de método tiene el poder de las [expresiones lambda](/post/lambdas-en-c-sharp) y la sintaxis de consulta es más legible al ojo humano. 
  
Sin importar por cual nos decidamos, el compilador siempre toma las consultas en Linq y las transforma a métodos, puesto que **elegir una sobre otra es únicamente cuestión de gustos** y necesidad del programador ya que **no existe un beneficio en desempeño entre una y otra**. De hecho, la <a href="https://msdn.microsoft.com/en-us/library/bb397676.aspx" target="_blank">recomendación de Microsoft es</a>:  
  
> Usa sintaxis de consulta en donde sea posible y sintaxis de método cuando sea necesario.  
  
Yo no la sigo al pie de la letra, de nuevo: cuestión de gustos. Porque a mi parecer hay algunas consultas que son más sencillas de escribir y leer como métodos, como un simple filtrado con `Where`, o en ocasiones la combinación de ambas facilita la lectura.
  
## Proyecciones (Select, select) 
La finalidad de usar Linq es el hacer consultas, el resultado de nuestras consultas está definido por las proyecciones que hacemos sobre los datos, dichas proyecciones están definidas por la cláusula `select` o el método `Select`.  
  
Con ellos se puede modelar los resultados de una consulta en un [tipo anónimo](/post/anonimos-en-c-sharp) o en un tipo previamente creado por nosotros. Por ejemplo, en la consulta anterior, el resultado era un conjunto de elementos que tenían las propiedades  `int TeacherId` y `string Name`. Es posible que usando los métodos de Linq, muchas veces se omita la proyección y es cuando se realiza una "proyección implícita", mientras que usando consultas siempre es necesario hacer un *select*.  

Solo por decir, así como en la consulta se crea un tipo anónimo con

```csharp  
new { TeacherId = grouped.Key, Name = teacher.GivenName };
```  

Igual se pudo haber creado un objeto de `Teacher` con  

```csharp  
new Teacher { TeacherId = grouped.Key, GivenName = teacher.GivenName };
```  
  
## Operadores de agregación  
Promedios, sumas, mínimos... de todo. Usualmente metemos esto dentro de un `for` más unas cuantas variables auxiliares y tenemos el resultado, con Linq, eso se acaba.  

### Conteo (Count)     
Supongamos que queremos contar la cantidad de profesores cuyo nombre comienza con `L`, entonces debemos utilizar el método `Count` (no está disponible en la sintaxis de consulta), y se hace de la siguiente manera:

```csharp  
var profesoresConL = db.Teachers.Count(p => p.GivenName.StartsWith("L"));
```  
  
La llamada al método `Count` se puede hacer sin parámetros o pasando un `Func` que actúa como condicional  para indicar qué elementos dentro de la colección deben ser considerados para la cuenta.  
  
### Suma (Sum) 
¿Cuantos años en total suman los profesores que tienen menos de 30? fácil, con más Linq. En este caso es necesario aplicar una función de filtrado antes de hacer la sumatoria, el filtrado se puede hacer con la sintaxis de consulta, pero la sumatoria debe ser con la de método:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var edadMasMenos30 = db.Teachers
    .Where (t => t.Age < 30)
    .Sum (t => t.Age);
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var menosDe30Total1 = (from t in db.Teachers
                        where t.Age < 30
                        select t.Age).Sum ();
```  
</div>  
</div>  

### Mínimos y máximos (Min, Max) 
No todos los profesores tienen correo electrónico, ¿tendrá algo que ver con la edad? con los métodos `Min` y `Max` podemos obtener los valores máximos y mínimos dentro de un conjunto de resultados:  
  
```csharp  
var edadMinSinEmail = (from t in db.Teachers
                        where t.Email == ""
                        select t).Min (t => t.Age);

var edadMaxSinEmail = db.Teachers
    .Where (t => t.Email == "")
    .Max (t => t.Age);  
```  
  
### Promedio (Average) 
Mmm... ¿Qué tal si probamos con los promedios de edad?, podemos usar el método `Average` para obtenerlo, primero filtramos los resultados para después obtener el promedio:  
  
```csharp  
var promedioEdadSMail = (from t in db.Teachers
    where t.Email == ""
    select t).Average (t => t.Age);

var promedioEmailCMail = db.Teachers
    .Where (t => t.Email != "")
    .Average (t => t.Age);
```  

También existe una función llamada `Aggregate` que nos da flexibilidad para integrar nuestra propia funcionalidad de agregación.

## Cuantificadores  
Otro lugar en donde metíamos un `bool`, un ciclo `for`, un condicional y problema resuelto. Usando Linq lo único que necesitamos es la condición. 
  
### Todos los elementos (All) 
Podemos usar el método `All` para verificar que todos los elementos dentro del conjunto cumplen con la condición especificada, **si todos cumplen la condición**, el método nos regresará `true`. Por ejemplo, si quisiéramos saber si todos los maestros tienen más de 18 años:  
  
```csharp  
bool todosMayores = db.Teachers.All(teacher => teacher.Age > 18);
```  

### Algún elemento (Any)
Contrario a `All`, el método `Any` devolverá `true` cuando exista **al menos un elemento que cumpla con la condición** que se le pasa como argumento. Por ejemplo, si queremos saber si existe algún maestro que se llame Cosme Fulanito:  
  
```csharp  
bool existeCosmeFulanito = db.Teachers.Any(teacher => teacher.GivenName == "Cosme"
                            && teacher.LastName == "Fulanito");
```  

Otra de las funciones de `Any` es evitarnos el "molesto" condicional de `.Count > 0`. Porque si llamamos `Any` sin pasar ningún condicional, este nos devolverá `true` siempre que nuestra secuencia contenga algún elemento. Digamos que antes de seguir queremos saber si hay clases registradas:
  
```csharp  
bool hayClases = db.Lectures.Any();  
```  

## Ordenamiento (OrderBy, orderby) 
Con Linq también es posible ordenar nuestros conjuntos de datos. Solo hay que recordar que generalmente el ordenamiento operará solo de manera temporal y que el ordenamiento no permanecerá una vez que hemos dejado de trabajar con los datos.

Para ordenar podemos usar tanto la cláusula `orderby` y especificarle la propiedad a usar como referencia o el método `OrderBy` y usar una expresión lambda para el mismo fin. Vamos a ordenar a los maestros por la longitud de su apellido: 

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var maestrosOrdenados1 = db.Teachers
    .OrderBy(t => t.LastName.Length);
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
var maestrosOrdenados2 = 
        from t in db.Teachers
        orderby t.LastName.Length
        select t;
```  
</div>  
</div> 

También tenemos disponible la cláusula `descending` y el método `OrderByDescending` que invertirán el ordenamiento, se usan así:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
.OrderByDescending(t => t.LastName.Length);
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
orderby t.LastName.Length descending
```  
</div>  
</div> 
 
¿Pero qué ocurre cuando queremos tomar en cuenta dos propiedades para realizar el ordenamiento? pues para eso podemos usar el método `ThenBy` o `ThenByDescending` para especificar otra condición de ordenamiento.

```csharp  
var maestrosOrdenados3 = db.Teachers
    .OrderBy(t => t.LastName.Length)
    .ThenByDescending(t => t.GivenName);
```  
  
## Operadores de partición  
Los operadores de partición son especialmente útiles para paginar los resultados, estos permiten considerar (o no considerar) cierta cantidad de elementos al inicio de la secuencia que los contiene.  
  
### Toma (Take)  
El método `Take` recibe un entero como parámetro que especifica la cantidad de elementos que son requeridos de toda la secuencia sobre la que opera, en caso de que haya menos de los requeridos únicamente devolverá los que encontró. Por ejemplo, si quisiéramos tomar los primeros 5 maestros más jóvenes, menores de 20 años:

```csharp  
var cincoMenores20 = (from t in db.Teachers
                      where t.Age < 20
                      orderby t.Age
                      select t).Take (5);
```  
  
### Salta (Skip)  
Podríamos verlo como el contrario de `Take`, ya que `Skip` igualmente toma un entero, pero este entero indica cuántos elementos de la secuencia debe ignorar antes de comenzar a tomar en cuenta. Supongamos que necesitamos tomar todos los maestros mayores de 30 años, comenzando por los mayores, pero ignorar a los primeros 4:

```csharp  
var otrosMaestros = db.Teachers
    .Where(t=> t.Age > 30)
    .OrderByDescending(t => t.Age)
    .Skip(4);
```  

También existe un método que se llama `TakeWhile` y otro que se llama `SkipWhile`, ambos requieren de una condición sobre la que se decidirá a partir de cuándo comenzar a tomar (o dejar de ignorar) los elementos de la secuencia.  

#### Lo que sigue  
Una vez que hemos visto el poder de Linq con estos ejemplos relativamente sencillos, no queda más que seguir y aprender sobre agrupamientos, uniones, intersecciones y otras cuantas cosas un poco más complejas. Lo visto aquí sigue sin ser lo más impresionante de esta herramienta. Recuerda que puedes encontrar el código fuente en la información del post. 