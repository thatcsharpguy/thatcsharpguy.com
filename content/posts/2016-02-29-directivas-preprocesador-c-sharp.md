---
layout: post
title: Directivas de preprocesamiento en C#
date: 2016-02-29 10:00:00
author: Antonio Feregrino
categories: c-sharp visual-studio
summary: Las directivas nos pueden ayudar a personalizar nuestro código a partir de condiciones dictadas antes de la compilación del mismo, o a forzar que ciertas condiciones se cumplan para que siquiera compile.
featured_image: featured.png
images_folder: directives/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/directives
tweet_id: 704480538824830977
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

A pesar de que C# no tenga un preprocesador de código dedicado (tal como lo tiene C), aún podemos hacer uso de algunas instrucciones que modifican el comportamiento del compilador, a estas palabras se les conoce como **directivas**.  
  
### Sintaxis  
La sintaxis de una directiva es simple: comienza por el símbolo `#` seguido inmediatamente por la instrucción para el compilador. Por ejemplo:

```csharp  
#define esta_es_una_directiva
#region Directivas
#if esta_es_una_directiva
#endif
#endregion
```  

### Instrucciones disponibles  
Podemos dividir las directivas dentro estas categorías:  

#### Definición de símbolos de compilación  
Primero que nada: En C#, un símbolo de compilación es una palabra (distinta de `true` o `false`) que es definida al momento de compilar el programa. Normalmente es el entorno en el que desarrollamos el encargado de definir dichos símbolos, por ejemplo, en Visual Sutdio cuando ejecutamos un programa en configuración Debug, el símbolo `DEBUG` es definido para nuestro código, o en Xamarin.Forms los símbolos `WINDOWS`, `IOS` y `ANDROID` son definidos dependiendo del proyecto que estamos compilando.

Pero nosotros también podemos definirlos mediante el uso de la directiva `#define [símbolo]` y borrar la definición mediante `#undef [símbolo]`. Mira el siguiente código:

```csharp  
#define HOLA
#define mundo
#define PIZZA
#undef mundo
```  

En este caso para el archivo quedarán definidos los símbolos `HOLA` y `PIZZA`, estas definiciones se hacen con una sola intención, que veremos más adelante. Hay que señalar que las definiciones deben ser las primeras instrucciones en cada archivo.

#### Compilación condicional    
Las directivas `#if`, `#else`, `#elif` y `#endif` permiten modificar el código que el compilador toma en cuenta para la compilación, funcionan de modo similar a un `if` en C#: `#if [condición]`, salvo que las condiciones simplemente son si determinado símbolo está definido o no, por ejemplo:

```csharp  
#if HOLA && mundo
Console.WriteLine("Hola mundo");
#elif HOLA
Console.WriteLine("Hola");
#elif mundo
Console.WriteLine("Hola");
#else
Console.WriteLine("...");
#endif

#if (HOLA || mundo) && PIZZA
Console.WriteLine("Hola o mundo y ¡pizza!");
#endif
```  

El resultado de la ejecución es
 
<pre>
Hola  
Hola o mundo y ¡pizza!
</pre>  
  
Los usos de la compilación condicional son muy variados, desde indicar que cierta porción de código únicamente se compile cuando estamos vamos a ejecutar una versión de prueba de nuestra aplicación hasta compilar distinto código fuente de acuerdo a la plataforma en la que se estará ejecutando la aplicación. 
  
#### Generar errores y warnings  
Podemos usar las directivas `#error` y `#warning` para hacer que el compilador "encuentre" errores y advertencias en nuestro código. La sintaxis es la siguiente: `#error [mensaje]` y `#warning [mensaje]`, por ejemplo:    

```csharp  
#warning It is dangerous to go alone!
#error Hola! soy un error de compilación
```  

Pero, ¿para qué querríamos provocar errores o *warnings* en nuestro código? yo creo que nadie. Sin embargo, usando la compilación condicional podemos "impedir" que el código se compile a menos que alguna configuración se cumpla, es por eso que normalmente estas directivas vienen rodeadas de `#if`, `#endif` o `#elif`:    

```csharp  
#if !DEBUG
#error Whoops, este código solamente puede ser usado en configuración debug
#endif
```  

#### Supresión de *warnings*  
Del mismo modo que podemos crear *warnings*, también podemos instruir al compilador a que ignore algunas de estas mediante el uso de la directiva `#pragma warning disable ([identificador de advertencias])`.  
  
Nunca ha sido bueno ignorar las advertencias en nuestro código ... sin embargo se me ocurren dos escenarios principales:  
  
 - Cuando accederemos al código de nuestro programa mediante reflexión y el compilador nos advierte que no estamos usando determinada propiedad o método.   
 - Cuando estamos implementando una interfaz y nuestro código únicamente llama a algunos métodos de la interfaz
 
 Cuando deshabilitamos el reconocimiento de advertencias con la directiva anterior, el efecto permanece hasta el fin del archivo, pero podemos deshabilitar dicho efecto usando la directiva `#pragma warning restore ([identificador de advertencias])`. Tomemos como ejemplo esta porción de código que como te podrás imaginar, provoca unas cuantas advertencias. 

```csharp  
int x = 0;
return;
int zero = 0;
if (1 != null) ;
```  

<img src="https://thatcsharpguy.github.io/postimages/directives__warnings.png" title="Warnings" />

Podemos deshabilitar las advertencias una por una:
```csharp  
#pragma warning disable 219
            int x = 0;
```  

Varias a la vez:

```csharp  
#pragma warning disable 162,219
            int zero = 0;
```  

O simplemente todas a la vez:

```csharp  
#pragma warning disable
            if (1 != null) ;
```  
  
#### Organización de código  
Estas son es seguramente las directivas con las que más frecuentemente te has encontrado, y es que su uso es mucho más común que las otras, su finalidad es agrupar físicamente bloques de código dentro de nuestro archivo fuente, además de que algunos IDE permiten colapsar el texto entre ella. Es importante decir que su inclusión no modifica el comportamiento del compilador.

Por ejemplo:  

```csharp  
#region Super bloque de código
Console.WriteLine("Hola");
#endregion
```  

Que en Visual Studio se ve así:

<img src="https://thatcsharpguy.github.io/postimages/directives__region.gif" title="Region directive" />

#### Para finalizar  
Como puedes ver, las directivas nos pueden ayudar a personalizar nuestro código a partir de condiciones dictadas antes de la compilación del mismo, o a "forzar" que ciertas condiciones se cumplan para que siquiera compile. También nos ayudan a organizar nuestro código en bloques físicos dentro del archivo fuente.  

