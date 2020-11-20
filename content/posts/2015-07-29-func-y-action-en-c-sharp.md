---
layout: post
title: Func y Action en C#
date: 2015-07-29 18:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Func y Action son moldes para crear delegados, podemos usarlos para evitarnos la fatiga de declarar un delegado para cada ocasión que sea necesario.
lang: es
ideone: http://ideone.com/fork/VmDLay
alias: /func-y-action-en-c-sharp/index.html
featured_image: featured.jpg
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Hace muy poco les hablaba de los <a href="/delegados-en-c-sharp">delegados en C#</a>, aprovechando este post quisiera hablarles un poco de la utilidad de estos. Como lo mencionaba, los delegados permiten a un desarrollador "inyectar" su propio código dentro de los métodos de el código escrito  por un tercero.   
  
  
Para poner un ejemplo, imaginemos que tenemos una lista de palabras ```palabras```  y un método ```RealizaOperacionSecreta``` que realiza cierta operación *secreta* sobre esa lista de palabras y las imprime en consola. Dicho método fue escrito por un desarrollador que no conocemos, pero que fue lo suficientemente amable para permitirnos elegir sobre qué palabras se realiza la operación mediante el uso de un delegado llamado ```filtro```.
```csharp  
public static void RealizaOperacionSecreta(List<string> palabras, Func<string, bool> filtro)
{
	foreach(string s in palabras)
	{
		if(filtro(s))
			Console.WriteLine(s.ToUpper());
		else
			Console.WriteLine(s);
	}			
}
```  
#### Func
Un momento... ese NO ES UN DELEGADO... ¿o si?. En realidad sí lo es, ```Func``` es un molde para crear delegados, podemos usar ese molde para evitarnos la fatiga de declarar un delegado para cada ocasión que necesitemos uno ```Func``` hace uso de los genéricos para especificar los parámetros de entrada y el tipo de retorno del delegado. En este caso, podemos decir que: ```delegate bool Filtro(string s)``` equivale a ```Func<string, bool>```.
Para dejar un poco más claras las cosas colocaré algunos ejemplos en código:
```csharp  
delegate int Cuenta(string a, int b); // Sería igual usar Func<string, int, int>
delegate char Separa(); // Es igual a usar Func<char>
delegate float Uhh(char a, int b, string c, double d); // Es igual a usar Func<char, int, string, double, float>
```  
Siempre el último tipo de dato especificado dentro de los ```< >``` será el tipo de retorno, por eso que si solo se especifica uno, este será el tipo de retorno del método. Podemos usarlo para crear delegados con hasta 16 parámetros de entrada.
  
Volviéndo al ejemplo... Podríamos entonces hacer uso del método de la siguiente manera:
```csharp  
public static void Main()
{
	List<string> palabras = new List<string> { "uno", "dos", "tres", "cuatro" };
	Console.WriteLine("Palabras mayores a 3 caracteres");
	RealizaOperacionSecreta(palabras, PorTamano);
	
	Console.WriteLine("Palabras que comienzan con u");
	RealizaOperacionSecreta(palabras, SoloConU);
}

public static bool PorTamano(string s)
{
	return s.Length > 3; 
}

public static bool SoloConU(string s)
{
	return s.StartsWith("u");
}
```  
El resultado de la ejecución es el siguiente, y es que está usando como filtros la función que le estamos pasando:
<pre>
Palabras mayores a 3 caracteres
uno
dos
TRES
CUATRO
Palabras que comienzan con u
UNO
dos
tres
cuatro
</pre>

#### Action  
Ahora, supongamos que además de dejarnos escoger el filtro, también nos deja especificar la acción a realizar, en este caso utilizando un delegado que tenga **void** como tipo de retorno. Un delegado con un tipo de retorno <code>void</code> puede ser reemplazado con un molde llamado <code>Action</code>. De tal manera que la implementación del nuevo método podría ser la siguiente:
```csharp  
public static void RealizaOperacionSecreta(List<string> palabras, Func<string, bool> filtro, Action<string> accion)
{
	foreach(string s in palabras)
	{
		if(filtro(s))
			accion(s);
		else
			Console.WriteLine(s);
	}			
}
```  
#### En resumen
<code>Func</code> es un molde para crear delegados de manera rápida, especificando los parámetros de entrada y el tipo de retorno mediante el uso de los ```< >```, <code>Action</code> cumple con la misma tarea, salvo que no tiene un tipo de retorno. En el siguiente post hablaré de las expresiones lambda una forma más elegante y simple de definir la implementación de delegados. Recuerda nuevamente que el enlace al código está en <a href="#post-meta">información del post</a>. En un post siguiente les hablaré de las expresiones lambda, otra manera de trabajar con delegados.

