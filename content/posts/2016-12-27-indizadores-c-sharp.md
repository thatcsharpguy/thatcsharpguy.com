---
layout: post
title: Sobrecarga de indizadores en C#
date: 2016-12-27 19:00:02
author: Antonio Feregrino
excerpt: Los indizadores en C# permiten dotar a tus tipos de dato de una forma más simple y expresiva para acceder a sus propiedades, además de ser una sintaxis con la que muchos desarrolladores ya están acostumbrados.
featured_image: indexers.jpg
images_folder: aprende-c-sharp/
tweet_id: 813874446897414144
lang: es
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Indizadores
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Seguramente estás muy, pero muy familiarizado con el tema del que hablaré en este post, y es que, ¿quién no ha utilizado algo así: `lista[0] = 9;` en sus programas?

A esta sintaxis se le coonoce como *indizadores*. Es muy común que la has visto antes en colecciones, como en arreglos y la clase `List`, sin embargo, es muy poco conocido que nosotros, como desarrolladores, podemos permitir que otros desarrolladores usen esta sintaxis en las clases que nosotros creamos.  

Podemos ver a los corchetes cuadrados como un operador de acceso a los elementos de determinada instancia, como es el caso de las listas, arreglos o diccionarios. Y aunque su uso se recomienda precisamente para eso (**acceso a los elementos de una colección**), podemos darle otros usos. Pero antes de ver cómo funcionan, vamos a ver la sintaxis.  

## Sintaxis  
Para implementar un indizador en nuestros tipos de dato tenemos que seguir una sintaxis que resulta un poco extraña cuando la vez:  

```csharp  

public int this[int index]
{
    get { /* Regresa un tipo entero */ }
    set { /* Establece el valor de "value" que es de tipo entero */ }
}   
```  

En general, para crear un indizador debemos escribir lo siguiente:

- El modificador de acceso (public, internal, private...)
- El tipo de dato del indizador.  
- La palabra reservada `this`. 
- Los parámetros del indizador entre corchetes cuadrados.
- La especificación del acceso mediante bloques: lectura con `get`, escritura con `set`.
- *Si especificamos `get` debemos regresar un valor en el bloque.
- *Si especificamos `set` tenemos a nuestra disposición el identificador `value` que hace referencia al valor que debe ser asignado.  

Supón que tenemos una clase especificada de la siguiente manera:  
```csharp  
public class Indexer
{
    public int this[string position]
    {
        get
        {
            Console.WriteLine("Accediendo a {0}", position);
            return 0; // Importante regresar un dato
        }
        set
        {
            // value contiene el dato que se va a establecer
            Console.WriteLine("Estableciendo {0} en la posición {1}", value, position);
        }
    }
```  

Que define un indizador de tipo `int` que como índice usa un tipo `string`. Entonces al ejecutar el siguiente código:  

```csharp  
var indizador = new Indexer();
var retorno = indizador["hola"];
indizador["feliz"] = 2017;
Console.WriteLine("Valor de retorno {0}", retorno);
```  

Obtendremos en la pantalla:

<pre>
Accediendo a hola
Estableciendo 2017 en la posición feliz
Valor de retorno 0
</pre>

Es importante notar que como parámetros del indizador podemos usar cualquier tipo de dato, no solo enteros o cadenas. Como en el siguiente ejemplo que se emplea un tipo `Indexer` como indizador y solo se implementa el bloque `get`:  

```csharp  
public string this[Indexer indexer]
{
    get { return indexer.ToString(); }
}
// ...
var otroIndizador = new Indexer();
Console.WriteLine(indizador[otroIndizador]); // Imprime: "Indizadores.Indexer"
```  

O en este otro ejemplo, en donde se usa un tipo `double` como parámetro para un índice del tipo `Indexer`. En él se implementa únicamente el bloque `set`:

```csharp  
public Indexer this[double position]
{
    set { Console.WriteLine("\"Insertando\" {1} en {0}", position, value); }
}
// ...
indizador[123.456d] = otroIndizador; // Mostrará en pantalla: "Insertando" Indizadores.Indexer en 123.456
```  

Podemos usar cualquier cantidad de índices, así como regresar cualquier tipo de dato, por ejemplo, podemos tener un indizador que acepte 3 parámetros de tipo `int`, `bool` y `double` y regrese una cadena:  

```csharp  
public string this[int i, bool b, double d]
{
    get { return String.Format("I'm an indexer: {0} {1} {2}", i, b, d); }
}

//

var resultado1 = indizador[10, false, 10];
Console.WriteLine(resultado1); // Imprime: "I'm an indexer: 10 False 5"
```  

En cierto modo podemos ver los indizadores como una especie de métodos con algunas restricciones, entre ellas la de que sus índices no pueden ser del tipo <a href="../out-ref-c-sharp" target="_blank"><code>out</code> o <code>ref</code></a>.  

Al final los indizadores terminan siendo transformados en métodos por el compilador, y es que solo son parte de la <a href="https://www.youtube.com/watch?v=Ypgw-TgT70I" target="_blank">azúcar sintáctica</a> que nos ofrece C#.

## ¿Por qué usarlos?  
Imagina que tienes una clase llamada `Alphabet` que almacena todas las letras de determinado alfabeto y para acceder a ellas nos ofrece un método llamado `CharacterAt(int position)` para acceder a determinado caracter dentro del alfabeto:

```csharp  
public class Alphabet
{
    List<char> caracteres;
    private Alphabet(params char[] caracteres)
    {
        this.caracteres = new List<char>(caracteres);
    }

    public char CharacterAt(int position)
    {
        return caracteres[position];
    }
    // ...

var griego = Alphabet.Greek;
var segundaLetra = griego.CharacterAt(2);
```  

Hay ocasiones en que esta sintaxis (usar un método) tiene mucho sentido y le da significado al programa que está escribiendo, sin embargo, hay otras en las que es mejor usar un indizador. Por ejemplo, ¿no crees que, si usáramos un indizador, nuestro código tendría más sentido?

```csharp  
var griego = Alphabet.Greek;
var segundaLetra = griego[2];
```  

El indizador para la clase `Alphabet` se ve así:

```csharp  
public char this[int i]
{
    get { return caracteres[i]; }
}
```  

## Ejemplos    
Además de los ya mencionados, en el framework de .NET y en algunos otros muy usados, los indizadores son uno de los elementos más empleados. Por ejemplo, uno de los lugares más comunes en donde aparecen es en los tipos de dato `string`.

Cuando trabajamos con WebForms, la variable `Session` también implementa un indizador que recibe una cadena para acceder a los valores que hemos almacenado en ella previamente.

Recuerda que puedes <a href="https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Indizadores" target="_blank">descargar el código fuente</a> de los ejemplos para que practiques con él, y que cualquier duda de la puedes hacer llegar usando los botones de contacto de abajo.