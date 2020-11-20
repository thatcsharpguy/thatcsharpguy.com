layout: post
title: ¿Qué hay en C# 7?
date: 2017-04-04 19:00:01
author: Antonio Feregrino
excerpt: Conoce algunas de las nuevas características incluídas en la versión 7 de C#;, tuplas, deconstructores, literales binarios y más.
featured_image: featured.jpg
images_folder: /aprende-c-sharp/siete/
github: https://github.com/ThatCSharpGuy/ElEspacio
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp

Hace ya más de un año que les hablé de las <a href="..\c-sharp-seis">características de C# 6</a>, pues bien, mientras esa versión del lenguaje era liberada para su uso, la versión 7 ya estaba en construcción. Ahora, ya tiempo después es posible para nosotros probar de manera segura esta nueva versión, lo único que necesitamos es tener Visual Studio 2017. 

En este post voy a mostrar algunas de las nuevas característias de este lenguaje, pero para comenzar a usarlas, hay que configurar el proyecto para que el compilador sepa que estamos trabajando con la nueva versión del lenguaje:  

#### Damos click derecho sobre el proyecto y abrimos las propiedades  

<img src="/images/aprende-c-sharp__siete__1properties.png" title=""Click derecho, propiedades"" />

#### En la sección de "Build" o "Compilación" abrimos "Avanzadas"  

<img src="/images/aprende-c-sharp__siete__2advanced.png" title=""Avanzadas"" />

#### Elegimos C# 7 como versión del lenguaje  

<img src="/images/aprende-c-sharp__siete__3selectlang.png" title=""Seleccionar lenguaje"" />

## Literales numéricos  
Una de las características más simples pero con un gran impacto es la inclusión del caracter `_` como separador de dígitos al momento de declarar literales numéricos. La idea detrás de esta característica es mejorar la legibilidad del código.

Y otra gran característica es la posibilidad de agregar de usar literales binarios, añadiéndole el sufijo: `0b`, con los cualest también podemos hacer uso de el separador de digitos:

```csharp  
private const double EarthDiameterInKms = 12_756;
private const int PaleBlueDotColor = 0xA0_DF_E6;
private const long Earth = 0b01000101_01100001_01110010_01110100_01101000;
```  

La alternativa es... bueno, no hay alternativa. En C# 6 no podemos usar el separador ni los literales binarios:

```csharp  
private const double EarthDiameterInKms = 12756;
private const int PaleBlueDotColor = 0xA0DFE6;
//const long Earth = 0b01000101_01100001_01110010_01110100_01101000;
```  

## *Tuples*  
Mientras que la versión anterior de C# ya daba soporte para *Tuplas*, este era un tanto limitado y hasta antinatural para el lenguaje. Usando C# 6 era necesario emplear directamente una <a href="..\genericos-c-sharp-clases">clase genérica</a>, sin embargo, ahora ya no es necesario. Ahora solo basta con usar paréntesis para agrupar un conjunto de posibles valores de retorno. Como en este caso, que se devuelven tres instancias de la clase `Coordenada` de un <a href="..\extension-methods-es">método de extensión</a>:

```csharp  
// CoordenadaExtensions.cs
public static (Coordenada antipoda, 
    Coordenada antipodaLongitud, 
    Coordenada antipodaLatitud) GetInterestingPoints(this Coordenada coordenada)
{
    return (coordenada.Antipoda(),
        coordenada.AntipodaLongitud(),
        coordenada.AntipodaLatitud());
}

// Program.cs
var interesingPoints = everest.GetInterestingPoints();
Console.WriteLine(
    $"Puntos interesantes del Everest:" +
    $"\n\t{interesingPoints.antipoda.Descripcion}" +
    $"\n\t{interesingPoints.antipodaLatitud.Descripcion}" +
    $"\n\t{interesingPoints.antipodaLongitud.Descripcion}");
```  

En este caso cada una de las "propiedades" de nuestra tupla tiene un nombre asignado desde la declaración del método, aunque bien pudimos haberlos dejado sin uno, al declarar el valor de retorno como `(Coordenada, Coordenada, Coordenada)`.

La alternativa en la versión anterior de C# es usar la clase `Tuple` y aprovechar el beneficio de los genéricos:  

```csharp  
// CoordenadaExtensions.cs
public static Tuple<Coordenada, Coordenada, Coordenada> 
    GetInterestingPoints(this Coordenada coordenada)
{
    return Tuple.Create(coordenada.Antipoda(),
        coordenada.AntipodaLongitud(),
        coordenada.AntipodaLatitud());
}

// Program.cs
var interesingPoints = everest.GetInterestingPoints();
Console.WriteLine(
    $"Puntos interesantes del Everest:" +
    $"\n\t{interesingPoints.Item1.Descripcion}" +
    $"\n\t{interesingPoints.Item2.Descripcion}" +
    $"\n\t{interesingPoints.Item3.Descripcion}");
```  

#### Importante  
Para usar las tuplas en C# 7 es necesario agregar el paquete de NuGet `System.ValueTuple` 

## Out parameters  
Los parámetros de tipo `out` llevan ya algo de tiempo entre nosotros, sin embargo, el escribirlos en nuestro código parece ser un poco redundante: primero los declaras (usualmente sin inicializarlos) y después los usas. Pero no más, en esta nueva versión del lenguaje se pueden declaran y usar en una misma instrucción, basta con especificar dos cosas en la llamada del método: 

 - Que es de tipo `out`
 - El tipo de dato al que corresponde  

Mira por ejemplo este método llamado `Deconstruct`  

```csharp  
// Coordenada.cs
public void Deconstruct(out decimal latitud, out decimal longitud)
{
    latitud = Latitud;
    longitud = Longitud;
}
```  

Para llamarlo en C# 7, basta con escribir el siguiente código:  

```csharp  
// Program.cs
everest.Deconstruct(out decimal evLat, out decimal evLong);
Console.WriteLine($"Latitud y longitud del Everest: {evLat} y {evLong}");
```  

Mientras que la alternativa es la de antes, declarar las variables y usarlas después:

```csharp  
// Program.cs
decimal evLat, evLong;
everest.Deconstruct(out evLat, out evLong);
Console.WriteLine($"Latitud y longitud del Everest: {evLat} y {evLong}");
```  

## Deconstructor  
Seguramente recuerdas el método `Deconstruct` de la sección anterior. Pues bien, los métodos llamados `Deconstruct` con parámetros de tipo `out` pueden ser usados de otra manera a través de una sintaxis parecida a las tuplas:  

```csharp  
(decimal evLat1, decimal evLong1) = everestCoordinates;
Console.WriteLine($"Latitud y longitud del Everest: {evLat1} y {evLong1}");
```  

Como puedes ver, estamos realizando una asignación múltiple desde un solo objeto. Las claves para que esto funcione son dos:  

 - La clase de dicho objeto tiene un método (o existe un método de extensión para dicha clase) llamado `Deconstruct` cuyos argumentos son del tipo `out`  
 - Los argumentos de el método `Deconstruct` coinciden en número y tipos de dato coinciden con los de un constructor de la clase  

En el código anterior la clase `Coordenada` tiene dos constructores:  

 - `public Coordenada(decimal latitud, decimal longitud)`
 - `public Coordenada(decimal latitud, decimal longitud, decimal altitud)` 

Y tiene dos *"desconstructores"*:  

 - `public void Deconstruct(out decimal latitud, out decimal longitud)`
 - `public static void Deconstruct(this Coordenada coordenada, out decimal latitud, out decimal longitud, out decimal altitude)` (método de extensión)

## *Expression bodied everything* y *throw expressions*    
Junto a la versión anterior llegaron los *expression bodied members*, pero solo llegaron a unas cuantos miembros de una clase. Ahora los tenemos disponibles en más lugares, como en constructores de clase, finalizadores y, como en el siguiente ejemplo, en los *accessors* para propiedades calculadas:  

```csharp  
private string _name;
public string Name
{
    get => _name;
    set => _name = value ?? throw new ArgumentException($"{nameof(value)} cannot be null");
}
```  

Espera, pero aún hay más, ¿ves la expresión que está después del <a href="..\null-coalescing">operador null coalescing</a>? sí, estamos lanzando una excepción dentro de una expresión. Esto en C# 6 no está permitido, ya que el lanzamiento de una excepción no podía ser tratado como una expresión.  

La alternativa es hacer uso de los *accessors* tradicionales, y de lanzar la excepción como convencionalmente se hace:  

```csharp  
private string _name;
public string Name
{
    get { return _name; }
    set
    {
        if (_name == null)
            throw new ArgumentException($"{nameof(value)} cannot be null");
        _name = value;
    }
}
```  

## Local methods  
Otra de las nuevas adiciones al lenguaje es la posibilidad de crear métodos que existan solo dentro de otros. Lo interesante de poder hacer esto es que estos métodos locales se comportan como métodos tradicionales y en ellos podemos hacer uso de *generics*, params y los argumentos del tipo `out` y `ref`. Un método local se puede declarar en cualquier lugar del código de otro, y se usa como usarías cualquier otro método:  

```csharp  
public static Coordenada AntipodaLongitud(this Coordenada actual)
{
    var newLongitud = GetNewLongitud(actual.Longitud);
    return new Coordenada(actual.Longitud, newLongitud);
    decimal GetNewLongitud(decimal longitud)
    {
        return (longitud < 0 ? 1 : -1) * Math.Abs(180 - Math.Abs(longitud));
    }
}
```  

Hay un par de alternativas en C#, siendo la más obvia la de crear un método privado y hacer uso de este:

```csharp  
public static Coordenada AntipodaLatitud(this Coordenada actual)
{
    var newLatitud = -actual.Latitud;
    return new Coordenada(newLatitud, actual.Longitud);
}

private static decimal GetNewLongitud(decimal longitud)
{
    return (longitud < 0 ? 1 : -1) * Math.Abs(180 - Math.Abs(longitud));
}
```  

Otra de las alternativas es hacer uso de <a href="..\func-y-action-en-c-sharp" target="_blank">Func y Action</a>, pero estos no te servirían para aprovechar los genéricos, ni los `params`... sí, es un poco limitante.

## Más características  
C# 7 viene cargado con otras características que no mencioné en este post, pero que podrías ir revisando para que te prepares para cuando la "tecnología te alcance". Si quieres descargar el código para jugar con él, lo puedes encontrar en <a href="https://github.com/ThatCSharpGuy/ElEspacio" target="_blank">GitHub</a>. 

Si alguna vez has programado en lenguajes funcionales (F# por ejemplo), te darás cuenta de que C# está tomando muchas de sus características, haciéndolo así un lenguaje que tiene mayor rango de aplicaciones, lo que resulta en un beneficio para quienes desarrollamos con él. **¿Tú qué le añadirías o quitarías al lenguaje?**  
