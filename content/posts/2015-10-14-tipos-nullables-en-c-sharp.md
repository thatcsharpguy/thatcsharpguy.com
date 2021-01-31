---
layout: post
title: Tipos nullables en C#
date: 2015-10-14 20:30:00
author: Antonio Feregrino
categories: c#
summary: Los tipos por referencia son nullables por definición. ¿Qué implica que sean nullables? pues nada más y nada menos que puedan tener un valor null o en términos prácticos, nungún valor.
lang: es
ideone: http://ideone.com/fork/9UsOI7
featured_image: featured.png
alias: /tipos-nullables-en-c-sharp/index.html
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

En un post anterior hablaba acerca de los tipos de dato en C#, y me doy cuenta que se me olvidó mencionar una característica importante de los tipos de dato, y para rectificar mi error voy a tomar la clasificación por referencia y valor de la que les platicaba.  

### Apuntando a la nada  
  
Los tipos por referencia son *nullables* por definición. ¿Qué implica que sean *nullables*? pues nada más y nada menos que puedan tener un valor `null` o en términos prácticos, nungún valor. Esto se puede explicar desde el nombre mismo, al ser referencias estas **pueden estar referenciando a un valor o a la nada** (a `null`).

Por otro lado, los **tipos por valor siempre tienen un valor** (valga la redundancia) asignado. Es así que un `int`, `bool`, `double`, etcétera nunca podrán valer *null*. Y es que estos no hacen referencia a ningún valor, sino que son el valor mismo.  
  
Muchas veces esta limitante nos lleva a inventarnos *números mágicos* como por ejemplo usar el <code>-1</code> para decir que un entero no tiene valor, o lógicas extrañas dentro de nuestro programa como decir que si un `DateTime` vale `DateTime.MinValue` es porque no tiene valor.  También nos complica un poco la vida cuando se trabaja con bases de datos en donde los datos pueden tener un valor o estar indefinidos.
  
### La estructura Nullable&lt;T&gt;  
Para nuestra fortuna, C# nos ofrece los *nullable types*, que son instancias de la estructura `Nullable<T>` (si no estás familiarizado con la sintaxis `<>` pronto la explicaré en un post, es maravillosa), donde `T` es un tipo por valor.  

Podemos ver esta estructura como una especie de caja para nuestros tipos por valor, dicha caja provee métodos para saber si contiene un valor y obtener el valor que contiene.  


#### Trabajando con Nullable&lt;T&gt;  
La sintaxis de estos tipos no es muy complicada, y es que además de la declaración convencional:  
```csharp  
Nullable<int> enteroNullable = null;
Nullable<double> puedeValerNull = 9.9;
```  
También podemos usar el símbolo (<code>?</code>) en la declaración de la variable para indicar que se trata de un tipo nullable:  
```csharp  
int? enteroNullable = null;
double? puedeValerNull = 9.9;
```  
Aunque en este punto debemos ser cuidadosos, ya que la sintaxis es un poco confusa, ya sabemos, <code>Nullable</code> es una estructura y las estructuras no pueden tener un valor `null`...  ¿entonces qué sucede al momento de asignar un valor a `enteroNullable` en  el código anterior?  
Como dije anteriormente, los tipos nullables son como una caja, al asignarle <code>null</code> estamos creando una caja que puede contener un <code>int</code> pero que al momento estamos dejando vacía. Podemos entonces trabajar con nuestra variable:  


<img src="https://thatcsharpguy.github.io/postimages/tipos-nullables-en-c-sharp/boxes.png" title="Tipos por valor" />

##### Obteniendo un el valor (o una excepción)  
Para obtener el valor contenido dentro de un *nullable*, tenemos a nuestra disposición la propiedad <code>Value</code>, que debemos usar con cuidado, puesto que en caso de que el *nullable* no tenga un valor asignado, lanzará una exepción del tipo `InvalidOperationException`. 
```csharp  
enteroNullable = null;
// int enteroExcepcional = enteroNullable.Value; 
// Este código lanza una excepcion ^

enteroNullable = 5;
int sinExcepciones = enteroNullable.Value;
```  

##### Comprobando si tiene un valor  
Si lo que queremos es evitarnos manejar exepciones, conocer si nuestra variable tiene un valor asignado con la propiedad `HasValue` 
```csharp  
if(enteroNullable.HasValue)
{
	Console.WriteLine("La caja está llena");
}
else 
{
	Console.WriteLine("La caja está vacía :(");
}
```  
  
##### Obteniendo un valor por default  
Si lo que de verdad nos interesa es obtener un valor de nuestra variable, podemos usar `GetValueOrDefaut`, que es un método que podemos usar para asegurarnos que no nos encontraremos con una exepción al consultar el valor. Lo que nos devolverá el método es:  
  
 - El valor contenido dentro del *nullable*, si tiene alguno
 - El valor por default del tipo de nuestro *nullable*, si este no tiene alguno o el valor que usemos como parámetro en nuestro método `GetValueOrDefaut`.
 
```csharp  
enteroNullable = null;

int defaultValue = enteroNullable.GetValueOrDefault();
// defaultValue == 0

int noTanDefaultValue = enteroNullable.GetValueOrDefault(8);
// noTanDefaultValue == 8

enteroNullable = 100; // Le asignamos un valor a la variable
int realValue = enteroNullable.GetValueOrDefault();
// realValue == 100
```  
  
<br />
Ahora sí: en futuros post seguré explorando algunos detalles escenciales de C#, como arreglos, métodos y clases.

