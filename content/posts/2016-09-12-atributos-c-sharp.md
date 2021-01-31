---
layout: post
title: Xamarin.Auth
date: 2016-09-12 19:00:01
author: Antonio Feregrino
summary: Los atributos nos ofrecen otra manera de interactuar con el código que escribimos, ya sea dándole información al compilador o extendiendo las definiciones de nuestros métodos, propiedades o clases en tiempo de ejecución.
featured_image: featured.png
images_folder: /aprende-c-sharp/attrs/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Attributes
tweet_id: 776040319494201344
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Hace tiempo contaba sobre las <a href="../directivas-preprocesador-c-sharp">directivas de preprocesador</a>, que no son más que **una forma de interactuar con las herramientas que compilan tu código**. Sin embargo, no es la única manera que tenemos para lograr esta tarea en C#, otra forma de hacerlo es a través de los *Atributos*.

Los atributos en C# no son más que una forma de agregarle información extra (metadatos) a nuestro código. Por poner un ejemplo, podemos usar un atributo para indicar que determinado método es obsoleto (`ObsoleteAttribute`) y no debe seguir usándose. Con otro atributo podemos indicarle a nuestro programa que determinada propiedad en un objeto es requerida (`RequiredAttribute`) o que el valor que esperamos es un correo electrónico (`EmailAddressAttribute`).  

## Uso  

Su sintaxis es un poco extraña, ya que a pesar de que un atributo no es más que una clase, generalmente no se instancía con el operador `new`, sino a través de los corchetes cuadrados `[]` y justo sobre la declaración de un método un método o tipo de dato, junto a un parámetro, o un ensamblado:  

```csharp  
[ObsoleteAttribute("Esta clase es obsoleta, prueba con iPhone 6 o Galaxy S6")]
public class Nokia3310
{
    [RangeAttribute(0.0,9990.99, ErrorMessage = "Wrong balance, must be between {1} and {2}")]
    public double Balance { get; set; }
    
    [Obsolete]
    public void Call(string number, [CallerMemberName]string whoIsCalling = null)
    {
        Console.WriteLine(whoIsCalling + " is calling " + number);
    }
    // ...
```  

Como puedes observar, en el código anterior existen 4 declaraciones de atributos: dos `ObsoleteAttribute`, un `CallerMemberNameAttribute` y un `RangeAttribute`. Antes de continuar, hay dos cosas a notar:  La primera es que no es necesaario escribir el nombre completo del atributo, tanto el nombre completo, como el nombre sin el sufijo "Attribute" son válidos para ser usados. Y la segunda es que los atributos también pueden recibir parámetros a través de su constructor.  

Como podrás ver, debido a su naturaleza declarativa, los atributos resultan ser algo muy legible para los programadores. A primera instancia podemos ver que tanto la clase `Nokia3310` como su método `Call` son obsoletos, que la propiedad `Balance` únicamente debe aceptar números entre 0 y 9990.99. Y que de no ser provisto, el valor de `whoIsCalling` será el nombre del miembro que <a href="ttps://msdn.microsoft.com/en-us/library/system.runtime.compilerservices.callermembernameattribute(v=vs.110).aspx#mainBody" target="_blank">llamó a la ejecución del método</a>.  

### En funcionamiento  

Pero su utilidad no termina ahí, si estamos desarrollando en Visual Studio, cuando deseemos usar la clase `NoKia3310`, el IDE nos informará sobre la obsolecencia de la clase o sus métodos: 

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp__attrs__vs.png" title="Capture" />

Y si luego ejecutamos un programa que lo use:  

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp__attrs__capture.png" title="Capture" />

## Parámetros  

Pero volvamos a los parámetros que reciben algunos atributos, los cuales son recibidos a través del constructor. Estos parámetros pueden categorizarse en dos grupos:  
  
 - Posicionales. Que para ser asignados correctamente requieren de ser declarados en el constructor, y cuando se especifican deben aparecer en la posición exacta en la que fueron colocados.
 - Nombrados. Que para ser asignados deben llevar antepuestos el nombre de la propiedad a la que se van a asignar. Estos no deben aparecer en el constructor.

En el caso de la primera declaración de `Obsolete` en el código anterior, estamos haciendo uso de un parámetro posicional, que representa el mensaje a mostrar cuando se presente la advertencia de miembro obsoleto. Mientras que en el caso de la declaración de `Range`, estamos haciendo uso de los parámetros tanto posicionales (los valores mínimo y máximo) como de parámetros nombrados (`ErrorMessage`).  

### Atributos "famosos"  
Los atributos son usados en diversas partes del framework de .NET, y no cabde duda que en algún momento te haz o te habrás de encontrar con alguno, entre los que más me he encontrado yo están:  

 - `Obsolete`.
 - `Flags`, que expliqué en <a href="../c-sharp-enums" target="_blank">el post sobre <code>enum</code></a>.
 - `JsonProperty` y los que se usan para la <a href="http://www.newtonsoft.com/json" target="_blank">serialización en JSON</a>.
 - `XmlElement` y los que se usan para la <a href="https://msdn.microsoft.com/es-es/library/system.xml.serialization.xmlelementattribute(v=vs.110).aspx" target="_blank">serialización XML</a>.
 - `Required` y todos los contenidos en el <a href="https://msdn.microsoft.com/en-us/library/dd901590(VS.95).aspx" target="_blank"><em>namespace</em> DataAnnotations</a>.
 - `HttpPost`, get, put... y todos los demás que especifican los <a href="https://docs.asp.net/projects/api/en/latest/autoapi/Microsoft/AspNetCore/Mvc/HttpPostAttribute/" target="_blank">verbos HTTP en Web API</a>.

## Creación  
Una vez que ya conocemos un poco del uso y funcionalidad de los atributos en C#, tal vez valga la pena saber cómo es que podemos crear nuestros propios atributos para que nosotros (o nuestro equipo de trabajo) se beneficie con ellos. Para este ejemplo vamos a crear un atributo para validar la compañia con la que un teléfono funciona.

### Nombre 
A peser de que el nombre no necesariamente debe tener la palabra "Attribute" (nuestra clase podría llamarse `ValidCarrier`), aunque es una convención que lo lleve. Por estas convenciones, nosotros le pondremos como nombre `ValidCarrierAttribute`.

### Derivar de la clase Attribute  
Este es sin duda el paso más importante, tu atributo debe derivar de la clase `Attribute`, en nuestro caso estaremos derivando de la clase `ValidationAttribute` que ya tiene como clase base a `Attribute`:  

```csharp  
public class ValidCarrierAttribute : ValidationAttribute
{
    // ...
```  

### Especificar su objetivo  
Una vez especificado el nombre del atributo y su relación con la clase base, es momento de especificar su objetivo. El objetivo de un atributo se refiere a en qué parte del código puede ser utilizado, para hacerlo es necesario utilizar el atributo `AttributeUsage`, que recibe como parámeto una combinación de valores del *enum* `AttributeTargets`. En nuestro caso, queremos aplicarlo únicamente a las propiedades de un tipo, por lo tanto usaremos únicamente `AttributeTargets.Property` (la lista completa en <a href="https://msdn.microsoft.com/en-us/library/system.attributetargets(v=vs.110).aspx" target="_blank">este enlace</a>):  

```csharp  
[AttributeUsage(AttributeTargets.Property)]
public class ValidCarrierAttribute : ValidationAttribute
// ...
```  

### Parámetros
Si tu atributo requiere recibir parámetros, debes declararlos en el constructor del atributo. En nuestro caso, recibiremos una cadena de texto que indicará las compañías con las que funciona, así como un parámetro que indica si se permite que un teléfono funcione con más de una compañía a la vez:  

```csharp  
//			   Positional parameter vvvvvvvvvvvvv
public ValidCarrierAttribute(string validCarriers)
{
    _validCarriers = validCarriers;
}

// Named parameter 
//          vvvvvvvvvvvvvvvv
public bool MultipleCarriers { get; set; }
```  

#### Especificar la funcionalidad  
En este caso, en el que estamos derivando de `ValidationAttribute` debemos sobreescribir el método `IsValid`, puedes revisar la implementación aquí.

### Uso  
Ahora sí, podemos usarlo en nuestras clases:


<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class IPhone
{
    [ValidCarrier("Vodafone,Telcel,AT&T", 
        MultipleCarriers = true)]
    public string Carrier { get; set; }
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class Galaxy
{
    [ValidCarrier("Vodafone,Movistar,Sprint")]
    public string Carrier { get; set; }  
    
}
```  
</div>  
</div>  

Y lo podemos probar en nuestro código de la siguiente manera:

```csharp  
var iPhone = new IPhone();
iPhone.Carrier = "Telcel,AT&T";

var iPhoneContext = new ValidationContext(iPhone, serviceProvider: null, items: null);

if (Validator.TryValidateObject(iPhone, iPhoneContext, phoneResults, true))
    Console.WriteLine("Tu iPhone es válido");
else 
    Console.WriteLine("Tu iPhone es inválido");

var galaxy = new Galaxy();
galaxy.Carrier = "Sprint,Movistar";

var galaxyContext = new ValidationContext(galaxy, serviceProvider: null, items: null);

if (Validator.TryValidateObject(galaxy, galaxyContext, phoneResults, true))
    Console.WriteLine("Tu Galaxy es válido");
else
    Console.WriteLine("Tu Galaxy es inválido");
```  

El resultado de ejecutar el código anterior es el siguiente:

<pre>
= Custom attributes  
Tu iPhone es válido  
Tu Galaxy es inválido  
</pre>  

En este ejemplo hacemos uso de todas las prestaciones que nos da la clase `ValidationAttribute`, aunque también pudimos haber creado un atributo desde cero, aunque para usarlo debemos conocer temas un poco más avanzados, como la reflexión o el uso de *Roslyn*.  

## Para terminar  

Eso es todo por este post, como puedes ver, los atributos sirven para una gran variedad de cosas: desde modificar el comportamiento del compilador hasta hacernos más sencillo el proceso de validación de datos en nuestras aplicaciones. A pesar de que su sintaxis es un poco diferente a lo que estamos acostumbrados en C#, no son difíciles de emplear y crear. Te invito a <a href="https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Attributes" target="_blank">descargar el código de este ejemplo</a>, y si tienes dudas, me las hagas llegar.