layout: post
title: Linq en C#
date: 2015-11-11 20:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Linq nos otorga la posibilidad de usar un lenguaje bastante similar al SQL dentro de nuestro código C# para consultar un origen de datos.
featured_image: featured.png
lang: es
ideone: https://ideone.com/fork/oE38uu
tags: AprendeCSharp, LINQ
featured_tag: AprendeCSharp

Es muy probable que alguna vez en tu vida de desarrollador hayas escuchado hablar de una consulta SQL, pero si no, te cuento que una consulta es una instrucción que se le da a *la computadora* para que busque registros dentro de una tabla y nos entregue los resultados de dicha búsqueda. Una consulta en SQL se ve así:
```sql  
SELECT Name, Surname, Age
FROM Students
WHERE Age > 10
ORDER BY Name
```  
Durante mucho tiempo, el poder de las consultas se mantuvo únicamente para las bases de datos, sin embargo, en el 2007 Microsoft liberó junto con la versión 3.5 de su framework, una herramienta llamada **LINQ** que proviene de **L**anguage **IN**tegrated **Q**uery (personalmente prefiero escribir Linq con minúsculas... cuestión de gustos).
<br />
<br />
Linq nos otorga la posibilidad de usar un lenguaje bastante similar al SQL dentro de nuestro código C# para consultar un origen de datos. Para poder usarlo, se requiere de un **proveedor** que no es más que una especie de traductor que toma nuestra consulta y la convierte en algo que pueda ejecutar el origen de datos. Existen proveedores dentro del framework que nos permiten usar Linq a objetos, Linq a XML y Linq a SQL.

### Linq to Objects  
Para esta serie de posts, me centraré en Linq a objetos (los otros tienen un funcionamiento similar) que opera sobre colecciones de objetos en memoria que implementan la interfaz <code>IEnumerable</code> como arreglos, <code>List</code>, <code>Vector</code>...   

Haciéndo una analogía a la consulta con la que inicié el post, supongamos que tenemos una lista de alumnos llamada <code>students</code>:
```csharp  
List<Student> students = new List<Student>
{
    new Student { Age = 11, Name = "Laura", Surname = "Rodríguez" },
    new Student { Age = 9, Name = "Francisco", Surname="Jímenez" },
};
```  
Formar una consulta simple es bastante sencillo, y muy parecido a lo que haríamos en SQL, lo que necesitamos hacer es:  

 1. Nombrar una variable que contendrá temporalmente cada uno de los elementos contenidos en la colección sobre la que vamos a ejecutar la consulta. Para nuestro caso, la llamarémos <code>s</code>.
 2. Indicar el origen de datos a consultar. En nuestro caso, este origen de datos es nuestra lista <code>students</code>.
 3. Indicar las condiciones que queremos que nuestra consulta compruebe.
 5. Realizar una acción con los elementos que cumplan las condiciones previamente indicadas.

Para ejecutar la consulta del inicio del post sobre nuestra lista de alumnos, debemos usar la sintaxis siguiente: 
```csharp  
var olderStudents = from s in students
                    where s.Age > 10
                    select s;
```  
Es fácil identificar las partes que la componen:  

 - <code>from s</code> hace referencia a la *variable temporal* de la que les contaba un poco antes.
 - <code>in students</code> hace referencia a la colección sobre la que vamos a ejecutar la consulta, esto hace que nuestra variable <code>s</code> sea de tipo <code>Student</code>
 - <code>where s.Age > 10</code> hace referencia a las condiciones que queremos que nuestra consulta compruebe aquí podemos usar condiciones más complejas usando los operadores lógicos <code>||</code> o <code>&&</code>.
 - <code>select s</code> hace referencia a la acción que queremos, en este caso únicamente estamos seleccionando los elementos que conicidan con <code>s.Age > 10</code>  
  

### Ejecución retrasada  
Una cosa que es importante saber es que el **declarar una consulta Linq no implica que se ejecute en el momento de la declaración**. Lo que quiere decir esto es que debemos ver el **resultado de una consulta como una referencia a cómo se ejecuta la consulta, no el resultado de la consulta ejecutada**, es por eso que muchas veces veran que se usa <a href="var-en-c-sharp">la palabra *var*</a> junto con Linq. En realidad la consulta será ejecutada cuando se necesite.  
  
Para explicar con un ejemplo, volviendo a nuestra consulta <code>olderStudents</code>, sabemos que solamente uno de los objetos cumple con la condición establecida de <code>s.Age > 10</code>, es por eso que cuando queremos contar los elementos con <code>Count()</code>, el resultado es <code>1</code>:
```csharp  
Console.WriteLine(olderStudents.Count()); // 1
```  
Ahora, si agregamos un nuevo elemento a nuestra lista original <code>students</code>:
```csharp  
students.Add(new Student { Age = 23, Name = "Antonio", Surname = "Feregrino" });
```  
Y volvemos a contar los objetos dentro de de nuestra consulta <code>olderStudents</code>, el resultado será <code>2</code>:
```csharp  
Console.WriteLine(olderStudents.Count()); // 2
```  
A esta característica se le conoce como **ejecución retrasada** o **deferred execution**. Podemos evitar esto al convertir nuestra consulta a una colección *estática*, como una lista o un arreglo utilizando los métodos que nos entrega Linq, como <code>ToList</code> o <code>ToArray</code>. Al hacer esto se ejecutará la consulta y se perderá la referencia a esta, con lo que cualquier modificación que sufran los orígenes de datos, no afectará nuestro resultado. Esto se logra así desde la declaración de la consulta, o más adelante llamando <code>ToList</code> o <code>ToArray</code> sobre la consulta.
```csharp  
// Al momento de la declaración
List<Student> olderStudentList = (from s in students
                                  where s.Age > 10
                                  select s).ToList();

// En cualquier otro momento		  
var olderStudentsArray = olderStudents.ToArray();
```  

#### Lo que sigue  
Las consultas simples, como la que se usa en este post, son solo la punta del iceberg de todo lo que se puede hacer con Linq, en futuros post veremos las posibilidades que tenemos para hacer consultas más complejas para obtener promedios, sumas, mínimos, hacer uniones, agrupamientos, intersecciones, distinciones... en fin, una enorme cantidad de cosas. Además de que veremos qué son los tipos anónimos en C#.