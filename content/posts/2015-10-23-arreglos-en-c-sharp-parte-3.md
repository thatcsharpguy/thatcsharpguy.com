---
layout: post
title: Arreglos en C# (multidimensionales rectangulares)
date: 2015-10-23 22:00:00
author: Antonio Feregrino
categories: aprende-csharp
summary: A diferencia de los arreglos escalonados, los rectangulares son lo más parecido a matrices que podemos encontrar en C#.
lang: es
ideone: http://ideone.com/fork/8C2iOj
featured_image: featured.png
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Después de conocer los <a href="/post/arreglos-en-c-sharp-parte-2">arreglos multidimensionales escalonados</a> esta vez toca el turno de conocer otro tipo de arrelgos multidimensionales en C#: Los rectangulares. En pocas palabras podríamos decir que estos no son "arreglos de arreglos", sino que en realidad son matrices dado que no es posible declarar arreglos dentro de ellos como con los escalonados.  
  
#### Instanciación  
Como con todos los tipos por referencia podemos usar la palabra reservada `new` para instanciar estos arreglos, aunque esta vez es necesario incluír una coma `,` para separar las dimensiones en lugar de los corchetes cuadrados de manera consecutiva, es decir, si quiero algo de dos dimensiones en lugar de `[][]` debemos escribir `[,]`:
```csharp  
char [,] gato = new char[3, 3];

string [,,] rubik = new string[3, 3 ,3];

int [,,,] laMatrix = new int[8, 2, 7, 6];
```  
Para este tipo de arreglos **siempre es necesario precisar el tamaño de todas las dimensiones**, ya que el espacio es asignado durante la asignación, a diferencia de los arreglos escalonados. El compilador nos indicará algún error si no utilizamos la misma cantidad de elementos para cada dimensión:
```csharp  
char [,] gato = // new char[3, 3] // El new es opcional
{
	{ 'x', 'o', 'x' },
	{ 'o', 'x', '-' },
	{ 'x', 'o', 'x' },
};

// Error de compilación, para 'gatoMalo' los arreglos internos deben ser de 2 elementos
char [,] gatoMalo = 
{
	{ 'x', 'x' },
	{ 'o', 'x', 'x' },
	{ 'o', 'x' },
};
```  
  
#### Acceso a los elementos  
Para acceder a los elementos debemos recurrir a la combinación de `[]` y `,`, también es importante recordar que los arreglos están indizados en `0`.
```csharp  
Console.WriteLine( gato[0,0] ); // 'x'

gato[1,2] = 'y';
Console.WriteLine( gato[1,2] ); // 'y'
```  

#### Propiedades y métodos  
Al contrario de los otros arreglos, en este caso una llamada a la propiedad `Length` nos devolverá la cantidad **total** de elementos dentro del arreglo, en lugar de la cantidad de elementos dentro de esa dimensión, por ejemplo:
```csharp  
Console.WriteLine(gato.Length); // 9 = 3 x 3

Console.WriteLine(rubik.Length); // 27 = 3 x 3 x 3

Console.WriteLine(laMatrix.Length); // 672 = 8 x 2 x 7 x 6
```  
Si lo que queremos es conocer el tamaño de determinada dimensión, es necesario hacer una llamada al método `GetLength` e indicando la dimensión a consultar
```csharp  
Console.WriteLine( "D0: " + laMatrix.GetLength(0) ); // 8
Console.WriteLine( "D1: " + laMatrix.GetLength(1) ); // 2
Console.WriteLine( "D2: " + laMatrix.GetLength(2) ); // 6
Console.WriteLine( "D3: " + laMatrix.GetLength(3) ); // 7
```  

#### Lo que sigue  
En siguientes posts conitnuaré hablando sobre C#, en particular de clases, métodos y parámetros, ¿quieres conocer sobre algo en específico? mándame un tweet o un correo electrónico ;)