---
layout: post
title: Espacios de nombres en C#
date: 2017-01-24 19:00:01
author: Antonio Feregrino
summary: Los nombres de espacio son de esas cosas en el dasrrollo a los que casi no les prestamos mucha atención, pero que bien usados nos resuelven muchos problemas y nos ayudan a organizar nuestro código.
featured_image: namespaces.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/espacios
tweet_id: 824019081066139648
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

El post de hoy trata sobre algo que muchos desarolladores a veces damos por hecho y no le prestamos mucha atención cuando estamos escribiendo el código de nuestras aplicaciones.

Se trata de los *namespaces* o *espacios de nombres*, que no solo son esos líneas de código que vienen por default cuando creamos una aplicación nueva usando Visual Studio.

Siempre ha sido una necesidad de gran importancia el poder acomodar nuestro código en bloques lógicamente relacionados, de tal modo que quede organizado y sea fácil de leer, entender y luego de esccribir. Para realizar esta organización nos podemos ayudar de los *nombres de espacios*.

Para definir un *espacio de nombres* se usa la siguiente sintaxis:

```csharp  
namespace [nombre del namespace]
{
    // Alcance
}
```  

Por ejemplo, mira la siguiente declaración de uno:  

```csharp  
namespace That
{
```  

Los namespaces pueden ser anidados, es decir, uno dentro de otro, así que puedes hacer cosas como esta:  

```csharp  
namespace That
{
    namespace C
    {
        namespace Sharp
        {
            namespace Guy
            {
                public class Uno { }
```  

Otra forma de anidación es la siguiente:

```csharp  
namespace That.C.Sharp.Guy
{
    public class Dos { }
}
```  

O realuzar cualquier combinación:  

```csharp  
namespace That.C
{
    namespace Sharp.Guy
    {
        public class Tres { }
    }
}
```  

A final de cuentas todos los tipos terminan siendo accesibles de la misma manera:

```csharp  
That.C.Sharp.Guy.Dos dos;
That.C.Sharp.Guy.Uno uno;
That.C.Sharp.Guy.Tres tres;
```  

## Reusar espacios de nombres

Es importante señalar que aunque no hay nada que nos impida usar los *espacios de nombres* del framework de .NET, como en el ejemplo siguiente:  

```csharp  
namespace System.Collections.Generic
{
    public class OtraColeccion
    {
    }
}
```  

Debes pensar dos veces al hacerlo, ya que a pesar de que funciona:  

```csharp  
using System.Collections.Generic;

class MainClass
{
    static void Main(string[] args)
    {
        OtraColeccion coleccion = new OtraColeccion();
```  

Existe el potencial de confundir a los usuarios de tu código al hacerles creer que el framework contiene tipos de dato que en realidad no.

## Alcance de los `usings`  
Los *espacios de nombres* nos ayudan también a establecer el alcance de otros *nombres de espacios* importados mediante la instrucción `using`, ya que podemos escribirlos dentro de la declaración de uno de ellos, indicando que ese `using` será válido solo dentro de su alcance: 

```csharp  
namespace Alcance
{
    using System;
```  


## El alias `global` 
Puede que mientras estamos desarrollando una app, te encuentres con la necesidad de escibir código que tenga los mismos identificadores que algúna otra porción existente, como es el caso de el siguiente ejemplo:

```csharp  
namespace Spaceship
{
    namespace System
    {
        public class Console
        {
            void TurnOn()
            {
                //Console.WriteLine("La consola fue encendida");
```  

Como podrás observar, en el código anterior se definen un *espacio de nombres*: `Spaceship` y `System`, este último contiene la clase `Console`... ahora, imagina que dentro del método TurnOn queremos informar al usuario que la consola está encendida. Pero no podemos usar directamente el método `Console.WriteLine`, porque el compilador piensa que nos estamos refiriendo a **nuestra** clase `Console`.  

Pero no te preocupes, para este tipo de casos extremos ex que existe el alias `global` que en pocas palabras *reestablece* los alcances de los namespaces, permitiéndonos así hacer uso de -casi- cualquier espacio de nombres, sin importar que existan conflicto entre ellos. Para escribir a consola dentro del método anterior simplemente tenemos que reescribir la línea como sigue:  

```csharp  
global::System.Console.WriteLine("La consola ha sido encendida");
```  

## Eligiendo un nombre de espacio  
Además del orden lógico muchas veces los *namespaces* se usan también para crear un "sentido" de pertenencia a quien creo el código. Muchas veces se sugiere que los nombres de espacio tengan la siguiente forma:  

`<Compañía>.(<Producto o Technología>)[.<Característica>][.<Nombres de espacio de código>]`

Como en `Microsoft.Web.Infrastructure`, aunque también hay exepciones como `System.IO.Stream`.



## ¿Necesito meter mi código en un *namespace*?  
No, no es necesario, puedes dejar todo tu código fuera de cualquier *namespace*, sin embargo esto no es tan recomendable por cuestiones meramente de organización. A menos que desemos que el tipo de dato creado esté disponibles globalmente en nuestro código.