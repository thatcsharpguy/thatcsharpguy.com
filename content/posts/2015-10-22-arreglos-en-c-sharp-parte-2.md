---
layout: post
title: Arreglos en C# (multidimensionales escalonados)
date: 2015-10-22 21:00:00
author: Antonio Feregrino
categories: aprende-csharp
excerpt: Los arreglos escalonados (o jagged arrays) son los tipos de arreglos multidimensionales más conocidos, y es que también son compunes en otros lenguajes de programación. Un arreglo escalonado no es más que un arreglo de arreglos.
lang: es
featured_image: featured.png
ideone: http://ideone.com/fork/0AAvgK
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

En el post anterior les contaba sobre los arreglos <a href="/post/arreglos-en-c-sharp-parte-1">arreglos unidimensionales en C#</a>, y hacia el final los invitaba a leer este, sobre los arreglos multidimensionales, así que sin más:  

Los arreglos escalonados (o *jagged arrays*) son los tipos de arreglos multidimensionales más conocidos, y es que también son compunes en otros lenguajes de programación. Un arreglo escalonado no es más que un arreglo de arreglos.

#### Instanciación  
Para crear estos arreglos de arreglos, tenemos una sintaxis similar a la creación de arreglos unidimensionales, con `[ ]` para cada dimensión: 
```csharp  
char[][] gato = new char[3][];

string [][][] rubik = new string[3][][];

int[][] escalera = new int[3][];
```  
Acá es importante notar que al instanciar un arraglo multidimensional de esta manera **únicamente estamos indicando el tamaño de la primera dimensión**, 3 en el caso de `gato`, 3 también para `rubik` y 2 para `escalera`. Es tarea nuestra inicializar los arreglos interiores: 
```csharp  
for(int i = 0; i < 3; i++)
{
	gato[i] = new char[3]; // gato[i] hace referencia a un arreglo
}
```  
```csharp  
for(int i = 0; i < 3; i++)
{
	rubik[i] = new string[3][];
	for(int j = 0; j < 3; j++)
	{
		rubik[i][j] = new string[3];
	}
}
```  
Con los arreglos escalonados no hay nada que nos prohíba crear arreglos internos de dimensiones iguales a la de arreglo que las contiene, es más, **podemos crear arreglos internos de distintos tamaños en la misma dimensión**, hagamos algo con nuestro arreglo `escalera`:   
```csharp  
escalera [0] = new int[1] { 1 };
escalera [1] = new int[2] { 2, 3 };
escalera [2] = new int[3] { 4, 5, 6 };
```  
Con estos arreglos de arreglos también podemos usar la instanciación de colecciones:   
```csharp  
char[][] gato = 
{
	new char[]{ 'o', 'x', 'o' },
	new char[]{ 'o', 'x', 'o' },
	new char[]{ 'o', 'x', 'o' }
};
```  
  
#### Acceso a los elementos  
De nueva cuenta, y por tratarse de arreglos, usaremos los `[ ]` para acceder a los elementos de nuestros arreglos, de tal manera que es posible escribir algo como esto:   
```csharp  
for (int i = 0; i < escalera.Length; i++)
{
	for (int j = 0; j < escalera [i].Length; j++) 
	{
		Console.Write (escalera[i][j] +  " ");
	}
	Console.WriteLine();
}    
```  

#### Lo que sigue
En el post siguiente les platicaré sobre los arreglos rectangulares, que son lo más parecido a matrices que podemos encontrar en C#. Estos son similares a los arreglos rectangulares con la pequeña diferencia de que no pueden contener arreglos de distintos tamaños dentro de la misma dimensión.