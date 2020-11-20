---
layout: post
title: Mejores pruebas unitarias con FluentAssertions
date: 2017-03-21 19:00:01
author: Antonio Feregrino
summary: Mejora tus pruebas unitarias con mensajes más claros, al momento de escribirlos y cuando tus tests fallan.
featured_image: featured.jpg
images_folder: /nugets/fluentassertions/
github: https://github.com/ThatCSharpGuy/UnitTesting
lang: es
tags: NuGetRecomendado, AprendeCSharp
featured_tag: NuGetRecomendado
---

La semana pasada <a href="../../tv/pruebas-unitarias">hablamos un poco</a> sobre las pruebas unitarias y los beneficios que estas tienen. También habamos sobre cómo es que podemos <a href="../../tv/pruebas-unitarias-vs">comenzar a crearlas</a> usando Visual Studio y C#. Y aprovechando la inercia, esta vez les traigo un <a href="../../tag/NuGetRecomendado">#NuGetRecomendado</a> que puede ayudarte a hacer las puebras unitarias más sencillas.  

En uno de los videos mencioné que una prueba unitaria usualmente se compone de tres pasos, y este paquete concierne directamente a la tercera: *Assert* o comprobar.  

 > Nadie desea que sus pruebas unitarias fallen, pero seguramente sí desean que, en caso de que fallen, les den la mayor información posible.

Ahora sí, mira este fragmento de código:  

```csharp  
string result = "That C# guy";

Assert.IsTrue(result.StartsWith("That"));
Assert.IsTrue(result.EndsWith("guy"));
Assert.IsTrue(result.Contains("Java"));
```  

Mediante los *asserts* se está corroborando que la cadena:  

 - Comience con "That"  
 - Termine con "guy"  
 - Contenga "Java"  

Ahora mira el siguiente usando *FluentAssertions*  

```csharp  
// Arrange
// Act
string result = "That C# guy";

// Assert
result.Should().StartWith("That").And.EndWith("guy").And.Contain("Java");
```  

En una simple "línea" se realizan varias comprobaciones sobre un mismo elemento, lo cual no solo es conveniente, sino que dada la naturaleza fluida de los métodos, el código se torna más legible y entendible a simple vista. Pero no solo eso, sino que los mensajes de error también se tornan más descriptivos para que el responsable de analizar los resultados de las pruebas las encuentre fácil de entender. Ya que el error mostrado es más descriptivo que si usáramos un `Assert` tradicional:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__normalf.png" title=""Normal 8 char test"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__faf.png" title=""FluentAssertions 8 char test"" />
</div>  
</div>  

### Should()  
La base de *FluentAssertions* es el método `Should` que en inglés significa *"debería"*. Sobre el valor de retorno de este se permite seguir encadenando métodos para hacer que nuestra prueba unitaria compruebe lo que deseamos, como por ejemplo en el siguiente bloque de código se comprueba la longitud de una cadena y se le añade un mensaje para mostrar en caso de que falle:  
```csharp  
// Normal unit test
string actual = "That C# guy";
Assert.AreEqual(8, actual.Length);

// With FluentAssertions
string result = "That C# guy";
result.Should().HaveLength(8, "because I love the number 8");
```  

Cuando la prueba falla:   

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__normal8chars.png" title=""Normal 8 char test"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__fa8chars.png" title=""FluentAssertions 8 char test"" />
</div>  
</div>  

### Arreglos  

También permite trabajar con arreglos. Nuevamente es necesario comenzar con `Should`. Para este caso se comprueba que el arreglo tenga una determinada longitud, sin embargo, también tiene opciones para comprobar el contenido del arreglo y demás propiedades de este:  

```csharp  
// Normal unit test
var array = new int[] { 10, 5 };
array.Should().HaveCount(3);

// With FluentAssertions
var array = new int[] { 10, 5 };
Assert.AreEqual(3, array.Length);
```  

Cuando la prueba falla:   

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__normalshouldhave3integers.png" title=""Normal should have 3 integers"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__fashouldhave3integers.png" title=""FluentAssertions should have 3 integers"" />
</div>  
</div>  

#### BeOfType  
Pero no solo funciona con arreglos y cadenas, sino que también permite trabajar con objetos. Por ejemplo, en el siguiente caso estamos realizando una comprobación sobre un `object`. Con *FluentAssertions* se corrobora que sea un arreglo de enteros y que dicho arreglo contenga 10 elementos.  

```csharp  
// Normal unit test
object thing = new int[] { 10, 5, 5 };
var arreglo = thing as int[];
Assert.IsNotNull(arreglo);
Assert.AreEqual(10, arreglo.Length);

// With FluentAssertions
object array = new int[] { 10, 5, 5 };
array.Should().BeOfType<int[]>()
    .Which.Should().HaveCount(10, "some weird reason");
```  

Cuando la prueba falla:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__normalarray10elements.png" title=""Normal should be array of 10 elements"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__faarray10elements.png" title=""FluentAssertions should be array of 10 elements"" />
</div>  
</div>  

### Dictionary  
*FluentAssertions* también puede comprobar el contenido de una colección, como un `Dictionary`, además de realizar la comprobación sobre alguna propiedad de el contenido de dicha colección:  

```csharp  
// Normal unit test        
var computerScientists = new Dictionary<string, string>();
Assert.IsTrue(computerScientists.ContainsValue("Grace Hopper"));
var value = computerScientists["Grace Hopper"];
Assert.AreEqual(12, value.Length);

// With FluentAssertions
var computerScientists = new Dictionary<string, string>();
computerScientists.Should()
    .ContainValue("Grace Hopper", "she's awesome")
    .Which.Length.Should().Be(12);
```  

Cuando la prueba falla:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__normaldictshouldcontain.png" title=""Normal dictionary"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__fadictshouldcontain.png" title=""FluentAssertions dictionary"" />
</div>  
</div>  

### Excepciones  

Otro gran uso de *FluentAssertions* es cuando es necesario probar que nuestro código lanza las excepciones correctas. Usualmente con C# puro tendríamos que marcar el método de la prueba con `ExpectedException`, mientras que con nuestro paquete de NuGet basta con usar el método `ShouldThrow`.

```csharp  
// Normal unit test
[TestMethod]
[ExpectedException(typeof(FormatException))]
public void Normal_ShouldThrowException()
{
    Action action = () =>
    {
        var i = 1;
        var t = 1 / (i - 1);
    };
    action();
}

// With FluentAssertions
[TestMethod]
public void FluentAssertions_ShouldThrowException()
{
    Action action = () =>
    {
        var i = 1;
        var t = 1 / (i - 1);
    };
    action.ShouldThrow<FormatException>("other reason");
}
```  

Cuando la prueba falla:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__normalexception.png" title=""Normal exception management"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/nugets__fluentassertions__faexception.png" title=""FluentAssertions exception management"" />
</div>  
</div>  

## Uso  
Recuerda que este es un paquete de NuGet que debe ser instalado en tu proyecto de pruebas, no en el proyecto que vas a probar. Basta con instalar  <a href="https://www.nuget.org/packages/FluentAssertions/" target="_blank">paquete de NuGet</a>.

```  
PM> Install-Package FluentAssertions
```  

O... o, siempre <a href="https://github.com/FluentAssertions/FluentAssertions" target="_blank">puedes descargar el código</a> fuente para integrarlo y hacerle las modificaciones que desees.

