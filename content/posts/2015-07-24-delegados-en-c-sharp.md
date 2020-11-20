---
layout: post
title: Delegados en C#
date: 2015-07-24 10:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Podemos ver un delegado en C# como un tipo de dato que apunta a un método permitiéndonos así extender las capacidades de nuestro código o permitir que otros lo hagan.
lang: es
featured_image: featured.png
ideone: http://ideone.com/fork/sXcTgo
alias: /delegados-en-c-sharp/index.html
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Para comenzar la explicación debemos comprender qué es un delegado. Podemos ver un delegado en C# como un tipo de dato que apunta a un método permitiéndonos así extender las capacidades de nuestro código o permitir que otros lo hagan. La forma de declarar un delegado es de la siguiente manera: ```<modificador de acceso> delegate <tipo de retorno> <nombre del delegado> (<lista de parámetros>)```. Podríamos, por ejemplo, tener un delegado que realice una operación con dos números enteros y devuelva un solo número entero. La defnición de este sería algo como:  

```csharp  
public delegate int Operacion(int a, int b);
```  

En ella, identificamos las partes:  
 - **public**, el modificador de acceso  
 - **delegate** la palabra clave para indicar que se trata de un delegado  
 - **int**, el tipo de retorno  
 - **Operacion**, el nombre del delegado  
 - **int a** e **int b**, los parámetros de entrada
 
#### Instanciación
Ahora la parte interesante, porque seguramente te preguntas: ¿cómo instancío un delegado? y ¿cómo defino el comportamiento de un delegado? Como mencioné arriba, el delegado es en cierta manera un apuntador a un método, luego entonces supongamos que tenemos varios métodos que comparten el mismo tipo de retorno y lista de parámetros que nuestro delegado. Para el ejemplo, tomemos los siguientes métodos:  

```csharp  
public static int Suma(int a, int b) 
{
	Console.WriteLine ("Ejecutando Suma");
	return a + b;
}
 
public static int Resta(int r, int t) 
{
	Console.WriteLine ("Ejecutando Resta");
	return r - t;
}
 
public static int Multiplicacion(int x, int y) 
{
	Console.WriteLine ("Ejecutando Multiplicacion");
	return x * y;
}
```  

Podemos ver que no importa el nombre del método ni de el de los parámetros que recibe. A partir de ellos se puede instanciar los delegados, para ello debemos utilizar el operador <code>new</code> seguido del nombre de nuestro delegado y como parámetro el método al que este "apunta". Para muestra:  

```csharp  
public static void Main()
{
	Operacion mas = new Operacion(Suma);
	Operacion menos = new Operacion (Resta);
	Operacion por = new Operacion (Multiplicacion);
```  

#### Ejecución
Para ejecutarlo basta con llamarlo como habitualmente llamaríamos al método al que hace referencia, es decir, el nombre asignado a la "variable" del tipo delegado y entre paréntesis la lista de parámetros, para este ejemplo los usaríamos de esta manera:  

```csharp  
	// 
	int r1 = mas(4,5); // r1 es igual a 9
	int r2 = menos(4,5); // r2 es igual a -1
	int r3 = por(4,5); // r3 es igual a 20
	//
```  

#### Combinación
Otra función útil de los delegados es la posiblidad de combinarlos, lo cual se realiza mediante el operador <code>+=</code>. Es confuso decir combinarlos, dado que la combinación es en realidad un tipo de encolamiento, haciéndo que se ejecute uno tras de otro, pero de manera independiente.  

```csharp  
	// 
	Operacion combinado = mas;
	combinado += por; // combinación con el operador +=
	
	int r4 = combinado(4,5); // r4 es igual a 20
}
```  

De ejecutarse el código anterior, en la consola se vería el texto: "Ejecutando Suma" seguido de "Ejecutando Multiplicacion" ya que ambos se ejecutan y el valor de <code>r4</code> es <code>20</code> porque siempre se respeta el valor devuelto por el último delegado ejecutado. 

#### Pasos siguientes
C# nos otorga plantillas para crear delegados con una serie personalizable de parámetros de entrada y de salida, a estos se les conoce como <code>Func</code> y <code>Action</code>, pero de ellos les hablaré en el siguiente post. Recuerda que el código de este post está disponible para que lo descargues, lo edites y juegues con él, búscalo en la <a href="#post-meta">información del post</a>
