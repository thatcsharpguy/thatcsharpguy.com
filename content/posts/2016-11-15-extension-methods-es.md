---
layout: post
title: Métodos de extensión en C#
date: 2016-11-15 19:00:01
author: Antonio Feregrino
excerpt: Los métodos de extensión nos permiten ampliar la funcionalidad de cualquier tipo de dato sin necesidad de tener el código fuente de este. A pesar de ser azúcar sintáctica, son muy útiles.
featured_image: extension.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/ExtensionMethods
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Seguramente conoces los <a href="..\metodos-c-sharp" target="_blank">métodos en C#</a>, pero has de saber que no es la "únicos" clase de métodos que podemos usar en este lenguaje. También existen otros llamados **métodos de extensión** o **extension methods**, que como su nombre lo indica nos ayudan a "extender" la funcionalidad de nuestros tipos de dato.  

Para comenzar, mira el siguiente código: 

```csharp  
var secuencia = new int[] { 1, 2, 3, 4, 5, 6 };
var resultado = Enumerable.Take(Enumerable.Where(secuencia, i => i % 2 == 0), 1);
```  

¿Te resulta familiar? (si no, te recomiendo que veas mi post sobre <a href="..\linq-en-c-sharp" target="_blank">linq en C#</a>). Seguramente recuerdas los métodos `Take` y `Where`, de linq salvo que esta vez los estamos usando con su sintaxis original. El resultado de lo anterior es idéntico a lo que obtendríamos de ejecutar el siguiente código:  

```csharp  
var resultado = secuencia.Where(i => i % 2 == 0).Take(1);
```  

Mucho más limpio y fácil de recordar.  

## Creándolos  
Imagina, por ejemplo, que por alguna razón extraña del destino, en tus apps necesitas cambiar las letras minúsculas por mayúsculas, una (mala) opción sería copiar y pegar el código, otra sería crear un método tradicional y compartirlo, o... ¡puedes crear tus métodos genéricos!

Para crear métodos de extensión es necesario lo siguiente:  

- Crear una clase estática para definir los métodos  
- Convertir el método deseado a estático con la palabra `static`  
- Anteponer la palabra `this` al primer elemento de la lista de parámetros del método  

Vamos a crear unas extensiones para `char`, comenzamos con la clase que los contendrá, yo acostumbro ponerle como nombre el tipo de dato más el prefijo `Extensions`:

```csharp  
public static class CharExtensions  
{
```  

Dentro de esa clase crearemos un método para "voltear" un caracter, es decir, para convertirlo a mayúscula si es minúscula y viceversa:

```csharp  
    public static char Flip(this char c)
    {
        return Char.IsUpper(c) ? Char.ToLower(c) :Char.ToUpper(c);
    }
```  

Ahora vamos a crear algo similar pero para cadenas:  

```csharp  
public static class StringExtensions
{
    public static string Flip(this string str, int n = Int32.MaxValue)
    {
        if (String.IsNullOrEmpty(str)) return "";
        var array = new char[str.Length];
        for (int i = 0; i < str.Length; i++)
        {
            if(i < n)
                array[i] = str[i].Flip();
            else
                array[i] = str[i];
        }
        return new String(array);
    }
```  

En el código de arriba puedes ver cómo es que ya comenzamos a hacer uso de nuestro método de extensión `Flip` para caracteres. También puedes ver que un método de esta clase puede aceptar más argumentos (en este caso `n`) además del elemento sobre el que se va a aplicar.

### El namespace
Para estos métodos, el *namespace* en el que estén ubicados es de vital importancia. Por ejemplo, si no declaramos `System.Linq` en los *usings* de nuestro código, no podremos hacer uso de los métodos `Where`, `Take`, etc. En el <a href="https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/ExtensionMethods" target="_blank">ejemplo anterior</a> el namespace de las extensiones es `Extensiones`, es por eso que debemos añadir `using Extensiones` para poder acceder a ellos desde cualquier parte del código.

Entonces ya podríamos usarlos en donde queramos:  

```csharp  
Console.WriteLine("HOLA mundo!".Flip()); // Imprime: "hola MUNDO!"
Console.WriteLine("HOLA mundo!".Flip(7)); // Imprime "hola MUndo!"
```  

### Miembros privados  
La sintaxis te puede hacer pensar que dentro de ellos puedes acceder a los miembros privados de las clases sobre las que operan, pero no es así. Para el método de extensión el tipo sobre el que funciona no es más que otro argumento, como veremos un poco más adelante.  

## Compatibilidad  
Puedes hacer estos métodos para cualquier tipo de dato, desde aquellos que son <a href="..\tipos-dato-c-sharp" target="_blank">por valor a los que son por referencia</a>, desde clases abstractas hasta interfaces, ah, y cómo olvidar a los tipos genéricos. 

## ¿NullReferenceException?  
Los métodos de extensión solo son azúcar sintáctica como te habrás dado cuenta, ya que cualquier llamada a un método de extensión se traduce a una llamada a un método estático dentro de una clase estática, pasando como primer argumento el elemento a la izquierda del operador `.`, esto se puede comprobar a través de un decompilador... o simplemente ejecutando el siguiente código:  

```csharp  
string n = null;
Console.WriteLine("null = " + n.Flip()); // Imprime: "null = "
```  

Si nosotros intentáramos llamar cualquier método de la clase `string` sobre la variable `n`, obtendríamos una `NullReferenceException`, sin embargo, con este tipo de métodos no es así, dado que **el método no se invoca sobre el objeto**, para él, nuestra variable `n` no es más que otro argumento.  

## Usos 

El ejemplo más claro del uso de los métodos es `System.Linq`. Sin embargo, como te podrás imaginar, los usos que les puedes dar son ilimitados, inclusive podrías crear tu propia biblioteca de métodos de extensión para reutilizar en tus proyectos... o reutilizar algunos que ya hacen uso de ellos como <a href="..\humaniza-tus-apps" target="_blank">Humanizer</a>, <a href="..\flurl" target="_blank">Flurl</a> o <a href="https://github.com/morelinq/MoreLINQ" target="_blank">MoreLINQ</a>.