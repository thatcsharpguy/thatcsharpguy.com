---
layout: post
title: Reflexión en C#
date: 2016-10-10 19:00:01
author: Antonio Feregrino
summary: La reflexión nos permite acceder a la información de un tipo en tiempo de ejecución. Cuando estamos escribiendo el código, somos nosotros quienes tenemos una idea general del sistema&#58; sabemos los tipos de dato de las variables, los métodos que contienen las clases, los atributos de los miembros de estas y un sin fin de cosas más.
featured_image: reflexion.jpg
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Reflexion
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

La reflexión en C# es solo otra de las caracterísitcas que el lenguaje nos entrega para desarrollar con él. No es una característica tan conocida por todos los desarrolladores y me atrevería a decir que de los que lo conocen, no todas la usan y es que no es necesaria usarla en todos los programas.

La reflexión nos permite acceder a la información de un tipo en tiempo de ejecución. Cuando estamos escribiendo el código, somos nosotros quienes tenemos una idea general del sistema: sabemos los tipos de dato de las variables, los métodos que contienen las clases, los atributos de los miembros de estas y un sin fin de cosas más. Otro elemento que conoce *todo* de nuestro programa es el compilador, ya que sabe si estamos usando los tipos correctos para llamar a los métodos o tenemos permiso para acceder a determinado miembro de una clase.

Sin embargo, una vez que se compila, el programa no tiene idea de los tipos de dato, métodos y demás complejidades del programa, por ejemplo, el siguiente programa no sabe que `zero` es del tipo `string`:

```csharp  
var zero = "0";
```  

No es sino hasta que empleamos la reflexión que el programa tiene acceso a esta información a través de un tipo `Type`:  

```csharp  
Type type = zero.GetType();
Console.WriteLine(type); // System.String
```  

También se puede acceder al ensamblado al que pertenece el tipo `String`:

```csharp  
Assembly assembly = type.Assembly;
Console.WriteLine(type.Assembly); // mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
```  

Y a partir de un ensamblado podemos obtener todos sus tipos con el método `GetTypes`, en este caso, además de obteneros, también los estamos filtrando, seleccionando solo aquellos cuyo nombre comience con "Int32":

```csharp  
foreach (var ty in assembly.GetTypes()
    .Where(ty.Name.StartsWith("Int32")))
{
    Console.WriteLine(ty);
}
```  

Otro de las cosas que podemos hacer empleando la reflexión, es instanciar tipos de dato a partir de una instancia de `Type` y la clase `Activator`:

```csharp  
var int32Type = assembly.GetType("System.Int32");

var createdInt  = Activator.CreateInstance(int32Type);
Console.WriteLine(createdInt); // 0
```  

Como ya habíamos visto antes, mediante los <a href="../atributos-c-sharp" target="_blank">atributos en C#</a> podemos proveer a los programas de metadatos, como es la clase `Smartphone` (cuya implementación puedes <a href="https://github.com/ThatCSharpGuy/aprende-c-sharp/blob/master/Reflexion/Smartphone.cs#L10" target="_blank">encontrar aquí</a>). 

El siguiente ejemplo de código obtiene todos los atributos de la propiedad `Carrier`:

```csharp  
var phone = new Smartphone();
phone.IsLocked = true;
phone.Carrier = "Entel";

var t = phone.GetType();

var carrierProperty = t.GetProperty("Carrier");

foreach (var att in carrierProperty.GetCustomAttributes())
{
    Console.WriteLine(att);
}
```  

El resultado de ejecutar el código anterior es:  

<pre>
RequiredAttribute
ValidCarrierAttribute
DisplayAttribute
</pre>

Que son los atributos que tiene la propiedad `Carrier`.

Y si queremos, podemos hacer cosas un poco más complejas. Por ejemplo, si deseamos encontrar todas las propiedades que tengan `DisplayAttribute` podemos buscarlas con <a href="../linq-en-c-sharp">Linq</a>:

```csharp  
var propertiesWithDisplayName = from prop in t.GetProperties()
                                where prop.GetCustomAttributes<DisplayAttribute>().Any()
                                select prop;
```  

Para luego mostrar los valores de una manera "amigable":

```csharp  
foreach (var property in propertiesWithDisplayName)
{
    var attr = property.GetCustomAttribute<DisplayAttribute>();
    Console.WriteLine(attr.Name + ": " + property.GetValue(phone));
}
```  

Ejecutar el código anterior dará como resultado:

<pre>
Compañía: Entel
Bloqueado: true
</pre>  

Ya para terminar, otra de las posiblidades que nos da la reflexión en C# es la de modificar los valores de una variable o propiedad en tiempo de ejecución con solo tener su nombre. Toma en cuenta el código siguiente, en donde se solicita al usuario ingresar el nombre de una propiedad y se busca dicha propiedad en el tipo `Smartphone`:

```csharp  
Console.WriteLine("Escribe el nombre de la propiedad a modificar:");
var propertyName = Console.ReadLine();
var propertyToModify = phoneType.GetProperty(propertyName);
```  

Después se verifica que la propiedad exista (el método `GetProperty` regresa `null` si no encuentra una propiedad con el nombre indicado) y si existe, solicitamos el nuevo valor para la propiedad:

```csharp  
if (propertyToModify != null)
{
    Console.WriteLine("Escribe el valor:");
    var value = Console.ReadLine();
    // Efectuar conversión ... 
}
else
{
    Console.WriteLine("La propiedad " + propertyName + " no existe");
}
```  

Para efectuar la conversión debemso recurrir, nuevamente, a la reflexión. A través del método `SetValue` y la clase `Convert`: 

```csharp  
propertyToModify.SetValue(phone, Convert.ChangeType(value, propertyToModify.PropertyType));
```  

## Usos de la reflexión
Si tu trabajo es el de ser un desarrollador de aplicaciones para usuario final, tal vez no le veas mucho uso a esta poderosa característica, sin embargo, usándola se puede

 - Acceder a los metadatos y conocer los atributos de cada uno de los componentes de un programa  
 - Instanciar clases en tiempo de ejecución  
 - Tener acceso a métodos, propiedades y cualquier otro miembro privado de los tipos de dato  

## Para finalizar  
Es importante señalar que en este post doy apenas una mínima introducción a lo que se puede hacer, esperando despertar tu interés. También debes saber que hacer uso de la reflexión hará que un programa se ejecute un poco más lento que si no se usara, ya que se realizan operaciones un poco más complejas.  
