---
layout: post
title: Dynamic y ExpandoObject
date: 2016-12-06 19:00:02
author: Antonio Feregrino
summary: Con dynamic convierte a C# en un lenguaje dinámico y fuertemente tipado (consulte restricciones).
featured_image: dynamic.jpg
images_folder: /aprende-c-sharp/
tweet_id: 806266786131968001
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Dynamics
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Cuando C# nació hace 16 años, nació como un lenguaje fuertemente tipado, y, mientras que lo sigue siendo, a partir de su versión 4.0 incorporó características que permitían usar tipado débil en él. 

Como sabrás, un lenguaje fuertemente tipado requiere que los tipos de las variables sean conocidos por el compilador al momento de compilar. Es decir, especificar que un entero es un entero, que una cadena es una cadena... por otro lado **que un lenguaje sea débilmente tipado significa que el compilador no sabe nada de las variables con las que está trabajando** y que será hasta que se estén ejecutando cuando se tenga conocimiento sobre ellas.  

En C# creamos un tipo de dato "débilmente tipado" con la palabra reservada `dynamic`, lo cual le indica al compilador que no debe preocuparse por esa variable. Por ejemplo:

```csharp  
dynamic entero = 0;
dynamic cadena = "Hola";
dynamic cadena2 = "mundo";
```  

Nosotros podemos inferir que `entero` es del tipo `int`, y que `cadena` y `cadena2` son *strings*, sin embargo, para nuestro compilador esto es desconocido ya que como son `dynamic` esto solo se resolverá hasta que el programa se esté ejecutando.  

Es por eso que al llamar al método *inexistente* `Abcdefg` dentro de la clase Int32, el compilador no genera ningun error: 

```csharp  
Console.WriteLine(entero.Abcdefg()); // RuntimeException
```  

Sin embargo, si ejecutamos esa línea obtendremos una excepción, indicandonos que dicho método no existe dentro de ese objeto.  

**En realidad los tipos que son marcados como `dynamic` si poseen un tipo asociado** solo que el compilador no conoce (y no le importa), y entonces asume que los objetos declarados de esta manera soportan cualquier operación.

Puedes comprobar que todos tienen un tipo de dato asociado si llamas al método `GetType` sobre los tipos de dato dinámicos:

```csharp  
Console.WriteLine(entero.GetType()); // Imprime "System.Int32"
Console.WriteLine(cadena.GetType()); // Imprime "System.String"
```  

## Compatibilidad con otros tipos de dato  
Estos tipos de dato son compatibles con otros... en tanto los tipos que "ocultan" sean compatibles, por ejemplo, podemos concatenarlos, confiando en que todos tendrá una implementación del método `ToString`:

```csharp  
string holaMundo = cadena + " " + cadena2+ " " + entero;
Console.WriteLine(holaMundo); // Imprime "Hola mundo 0"
```  

## Mutabilidad 
Las variables *dinámicas* pueden cambiar completamente su contenido sin causar ningún problema, por ejemplo, podemos convertir `entero` en una instancia de `MainClass`:

```csharp  
entero = new MainClass();
Console.WriteLine(entero.GetType()); // Imprime "Dynamics.MainClass"
```  

## ExpandoObject para crear tipos dinámicos
Nosotros también podemos crear tipos dinámicos haciendo uso de la clase `ExpandoObject` que es una clase "especial" que nos ofrece el framework. Es importante que lo declares como `dynamic` ya que de no hacerlo, el compilador lo tratará como fuertemente tipado

```csharp  
dynamic expando = new ExpandoObject();
//ExpandoObject expando = new ExpandoObject(); // Esto lo convierte en fuertemente tipado
Console.WriteLine(expando.GetType());  
```  

A partir de ahí podemos asignar propiedades de acuerdo a nuestras necesidades:  

```csharp  
expando.Clase = entero;
expando.HolaMundo = holaMundo;
expando.expando = expando;
```  

Al ser un tipo de dato en C# también se pueden usar como argumentos para un método o como tipos de retorno, por ejemplo, el siguiente:  

```csharp  
private static void WorkWithDynamic(dynamic d)
{
  Console.WriteLine(d.Clase + " : " + d.Clase.GetType());
  Console.WriteLine(d.HolaMundo + " : " + d.HolaMundo.GetType());
  Console.WriteLine(d.expando + " : " + d.expando.GetType());
}
```  

Entonces al llamar a `WorkWithDynamic(expando);`, obtendremos:

<pre>
Dynamics.MainClass : Dynamics.MainClass
Hola mundo 0 : System.String
System.Dynamic.ExpandoObject : System.Dynamic.ExpandoObject
</pre>

## Uso  
Como ya te imaginarás esto puede traer muchas ventajas pero a la vez muchos dolores de cabeza. En lo personal prefiero usar siempre tipado fuerte cuando desarrollo, pero si encuentro algunos casos en los que resulta muy útil:  

### En servicios web
Hay ocasiones en las que crear una clase para consumir un servicio web puede ser excesivo, este es otro de los usos que le podemos dar a los tipos dinámicos. Por ejemplo, si consultamos la PokéAPI, y usamos Newtonsoft.Json, podemos usar un tipo dinámico para acceder a sus propiedades: 

```csharp  
dynamic item = Newtonsoft.Json.JsonConvert.DeserializeObject(responseString);
```  

Ahora en `item` tendremos una versión de la respuesta en *json* pero en .NET, solo que el compilador no sabe. No es sino hasta que se ejecuta que podemos acceder a las propiedades. Si te fijas bien, los nombres son idénticos a lo que regresa la API:

```csharp  
Console.WriteLine("Names for " + item.name + " (datatype " + item.GetType() + ")");
foreach (var name in item.names)
{
  Console.WriteLine("\t" + name.language.name + ": " + name.name);
}
```  

Obtendremos la siguiente respuesta:  

<pre>
Names for master-ball (datatype Newtonsoft.Json.Linq.JObject)
	en: Master Ball
	it: Master Ball
	es: Master Ball
	de: Meisterball
	fr: Master Ball
	ko: 마스터볼
	ja: マスターボール
</pre>

### ViewBag en ASP.NET MVC  
Otro fantástico uso de esta característica es la propiedad `ViewBag` de la clase `Controller` en MVC, con ella podemos pasar información a la vista sin necesidad de crear un modelo muy específico para cada una de las pantallas de nuestro sistema.  

### Comunicación con otros lenguajes  
Cuando interactuamos desde C# con otros lenguajes como Python o JavaScript que involucran tipado débil y dinámico es necesario contar con un tipo similar dentro de C#, así que este es otro de sus usos.  

## Concluyendo
Como conclusión puedo decir que yo siempre trato de evitarlos, ya que pueden transformar el código en un desastre total si se usan sin control. Además de que pueden llegar a resultar confusos de emplear cuando los ves en un código no documentado o que no hayas escrito tú mismo. Habiendo dicho esto, acepto que sí tienen usos específicos y muy bien identificados, además de que si sabes lo que estás haciendo te pueden ahorrar tiempo de desarrollo cuando programas. 