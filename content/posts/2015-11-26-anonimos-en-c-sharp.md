layout: post
title: Tipos anónimos en C#
date: 2015-11-26 21:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Para cada tipo anónimo que usamos, el compilador crea una definición de clase para dicho tipo por nosotros, ahórrandonos así la tarea de tener que hacerlo.
featured_image: featured.png
lang: es
tweet_id: 708331999979679744
ideone: http://ideone.com/fork/WdN2kR
tags: AprendeCSharp
featured_tag: AprendeCSharp

¿A qué suena cuando se dice que algo es anónimo? … pues eso mismo es aplicable a los tipos anónimos en C#. En este post te voy a contar sobre ellos, pero antes comencemos por ver el siguiente fragmento de código:

```csharp  
class Student
{
    public string Name { get; set; }
}
```  

`Student` es un ejemplo de un tipo que **no es anónimo**, porque  pues bueno… tiene un nombre. Para usarlo basta con instanciarlo de esta manera: `var estudianteConocido = new Student() { Name = "Roberto" };` y listo.

## Ahora sí, anónimos

Ahora veamos un ejemplo de algo que **sí es anónimo** (y prepárate para leer muchas veces la palabra "anónimo"):

```csharp  
var estudianteDesconocido = new { Name = "Rigoberto" };
var estudianteDesconocido2 = new { Nombre = "Pedro", Edad = "12" };
```  

Pareciera una declaración normal pero es importante notar que no podemos decir de qué tipo es nuestra variable `estudianteDesconocido` o `estudianteDesconocido2` puesto que son anónimos, también por eso se <a href="/post/var-en-c-sharp/">usa var para declararlo</a>.  

Para cada tipo anónimo que usamos, el compilador crea una definición de clase para dicho tipo por nosotros, ahórrandonos así la tarea de tener que hacerlo. Es decir que un anónimo es un tipo por referencia que deriva directamente de `object`.

Estos tipos son una herramienta más que nos ofrece C# para minimizar la cantidad de código que nosotros como programadores tenemos que escribir, sin embargo es importante tener en cuenta algunas de sus características para sacarle el máximo provecho:  

### Inicialización  
Para inicializarlos, es necesario usar el operador `new`, la <a href="https://msdn.microsoft.com/en-us/library/bb397680.aspx" target="_blank">inicialización de objetos</a> y la palabra `var`. La inicialización de objetos cobra importancia con los tipos anónimos porque el nombre de sus propiedades depende del nombre de las variables que se asignen en su creación.  

Así pues:  

- `estudianteDesconocido` tiene una propiedad llamada `Name`  
- `estudianteDesconocido2` tiene una propiedad llamada `Nombre`
 y una llamada `Edad`  

Podemos ahorrarnos el indicar los nombres de las propiedades directamente si utilizamos variables para inicializar el tipo anónimo:

```csharp  
double x = 9.1;
float y = 3.2;

var point1 = new { x, y };
var point2 = new { x, SuperY = y };
```  

La variable `point1` tendrá una propiedad llamada `x` de tipo double y otra llamada `y` del tipo float. Inclusive podemos combinar las formas de inicializar, en este caso `point2` tiene como propiedades `x` y `SuperY`.

### No es tipado dinámico  
Una de las grandes dudas que te podrían surgir es si esto no viola el hecho de que C# es un lenguaje fuertemente tipado y pues no. Los tipos anónimos **siguen siendo fuertemente tipados**, como ya se dijo antes, al momento de la compilación se crea una clase para nuestra variable y esta variable no puede cambiar de tipo con el flujo del programa.  

Podemos comprobarlo fácilmente. El compilador nos marcará un error si intentamos hacer algo como esto:

```csharp  
// "Teacher" no es una propiedad de estudianteDesconocido
estudianteDesconocido.Teacher = "Andrés";
```  

O algo como esto:

```csharp  
// Son tipos diferentes
estudianteDesconocido = estudianteDesconocido2;
```  

### Propiedades de solo lectura  
Las propiedades que un *anónimo* contiene son siempre de solo lectura, es decir que son solo asignables al momento de la inicialización y no podemos cambiar sus valores en otro punto del programa; esto tiene una explicación que tiene que ver con la <a href="http://blogs.msdn.com/b/sreekarc/archive/2007/04/03/immutable-the-new-anonymous-type.aspx" target="_blank">inmutabilidad de los anónimos</a>.  

Explicado en código es decir que no podemos hacer algo como esto:

```csharp  
// Nay, las propiedades en un anónimo son de solo lectura
estudianteDesconocido.Name = "Nuevo nombre";
```  
 

### Restricciones  
Una de las restricciones más notables de los tipos anónimos es que su uso está limitado a un alcance interno, es decir, dentro del cuerpo de un método.  

Si bien es posible crear un método que devuelva `object` (que es la clase base de cualquier tipo anónimo), no podemos usarlo sin violar el tipado estático que provee C#.  

### Derivan de object  
Al ser tipos derivados de `object` los tipos anónimos tienen métodos que resultan bastante útiles, estos son:

#### ToString
Al llamar `ToString` el resultado será una cadena que contiene todas y cada una de las propiedades que contiene así como sus valores. Por ejemplo, llamar:

```csharp  
Console.WriteLine(estudianteDesconocido);
Console.WriteLine(estudianteDesconocido2);
Console.WriteLine(point2);
```  

Mostrará en pantalla `{ Name = Rigoberto }`, `{ Nombre = Pedro, Edad = 12 }`, `{ x = 9.1, SuperY = 3.2 }` respectivamente.  

#### Equals  
Es posible comparar los tipos anónimos contra cualquier otro tipo, sin embargo el método `Equals` únicamente devolverá `true` si los tipos comparten:  

 - Las mismas propiedades, en nombre y número
 - El mismo orden de declaración de las propiedades
 - Los mismos valores para esas propiedades  

Esto tal vez requiere un ejemplo en código, así que, considera las declaraciones de tipos anónimos siguientes:

```csharp  
var p1 = new { X = 1, Y = 0 };
var p2 = new { X = 1, Y = 0 };
var p3 = new { Y = 0, X = 1 };
var p4 = new { X = 5, Y = 2 };
```  

Todas tienen propiedades con nombre similar `X` y `Y`, pero únicamente `p1` y `p2` son iguales en propiedades y en valores. `p3` y `p1` son iguales en valores pero no en orden. `p1` y `p4` son iguales en orden pero no en valores.

```csharp  
Console.WriteLine(p1.Equals(p2)); // True
Console.WriteLine(p1.Equals(p3)); // False
Console.WriteLine(p1.Equals(p4)); // False
```  

### Uso  
Bueno... pero ¿para qué sirven?

#### LINQ  
Los anónimos son tipos que otorgan a <a href="/post/linq-en-c-sharp/">LINQ (otra de las herramientas de C#)</a> un gran poder, y es que usandola junto con las consultas se pueden crear tipos "al vuelo" para usarlos dentro de nuestro código, este es un ejemplo sencillo de uso:

```csharp  
var olderStudents = from s in students
                    where s.Age > 10
                    select new { FullName = s.Name + " " + s.Surname, s.Age };
```  

#### Debuguear  
Dada la facilidad y limpieza con la que se pueden declarar, los anónimos son comunmente usados para cuando se debugea, como recordamos, al llamar `ToString` en un anónimo, este nos mostrará una lista de sus propiedades junto con sus valores.  

### Lo que sigue  
En posts siguientes revisaremos consultas más complejas con LINQ y los tipos anónimos, además de otras de las características del lenguaje. Te invito a que revises las nuevas páginas de etiquetas en <a href="/tag">etiquetas del blog</a> y a que te estés al tanto de mis posts en <a href="/registro">el registro</a>. 
