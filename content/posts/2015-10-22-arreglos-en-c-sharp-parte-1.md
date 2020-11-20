---
layout: post
title: Arreglos en C# (unidimensionales)
date: 2015-10-22 21:00:00
author: Antonio Feregrino
categories: aprende-csharp
excerpt: En C# llamamos arreglo a una colección de objetos, dicha colección tiene longitud definida y esta no puede cambiar con el flujo del programa, esto podrá sonar limitante, pero al trabajar con arreglos se obtiene un mejor desempeño que con cualquier otra colección. Usando el lenguaje podemos crear, recorrer y manipular arreglos de cualquier tipo de objeto.
lang: es
featured_image: featured.png
ideone: http://ideone.com/fork/o0Sml3
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

En C# llamamos arreglo a una colección de objetos, dicha colección tiene longitud definida y esta no puede cambiar con el flujo del programa, esto podrá sonar limitante, pero al trabajar con arreglos se obtiene un mejor desempeño que con cualquier otra colección. Usando el lenguaje podemos crear, recorrer y manipular arreglos de cualquier tipo de objeto.

### Unidimensionales  
Comenzando por lo más básico, los arreglos unidimensionales son colecciones lineales, que nos pueden ayudar a representar una secuencia numérica, letras del alfabeto o un cromosoma dentro de un programa de algoritmos genéticos, entre muchas otras otras.  
  
#### Instanciación  
Para crear un arreglo haremos uso de la palabra reservada `new` y los corchetes cuadrados `[ ]`, también es necesario conocer el tamaño que necesitaremos, puesto que como ya lo mencioné, no es posible cambiar el tamaño una vez creado: 
```csharp  
char [] vocales = new char[5];

int [] conteo = new int[10];

object [] misObjetos = new object[3];
```  
Si creamos arreglos de esa manera, cada posición tendrá el valor por default del tipo de dato del arreglo... bueno, mejor usamos nuestro ejemplo:  

- El arreglo de `vocales` contendrá 5 `\0`, que es el valor por default de un `char`.    
- El arreglo `conteo` contendrá 10 `0`, que es el valor por default de un entero.  
- El arreglo `misObjetos` contendrá 3 `null`, que es el valor por default de un `object`.  

<br />  
Además de la instanciación tradicional, también podemos emplear la instanciación de colecciones, la cual nos permite inicializar un arreglo asignándole valores inmediatamente:  
```csharp  
char [] vocales = new char[5] { 'a', 'e', 'i', 'o', 'u' };

int [] conteo = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 0 };

object [] misObjetos = { "3", 1, 99.99 };
```  
Como podemos ver, tenemos varias opciones para inicializar arreglos unidimensionales:  

1. `new char[5] { ...`, nos permite indicar el tamaño y el tipo explícitamente y posteriormente indicar los valores, si cambiáramos el `5` por un `7`sin aumentar la cantidad de valores obtendríamos un error de compilación.  
2. `new int[] { 1, 2, ...`, nos permite indicar solamente el tipo de dato de los elementos, la cantidad es inferida por el compilador, en este caso podemos incrementar o reducir la cantidad de elementos al momento de inicializar sin ningún problema.
3. `{ "3", 1, 99.99 }`, al inicializar de esta manera estamos dejandole al compilador la tarea de inferir tanto el tipo de dato del arreglo como la cantidad de elementos que contiene. Dicho sea de paso, el declarar un arreglo así puede resultar un poco confuso de leer.  

#### Acceso a los elementos  
Una vez instanciado, podemos acceder a los elementos del arreglo usando nuevamente los corchetes cuadrados `[ ]` y el índice del elemento al que queremos acceder. Nota importante **los arreglos están indizados en 0** es decir, el primer elemento de un arreglo está en el índice 0.  

Retomemos los arreglos del ejemplo pasado. Para acceder a la `a` dentro del arreglo `vocales` debemos acceder a la posición `0`:  
```csharp  
Console.WriteLine(vocales[0]); // a
```  
O, digamos que queremos reemplazar la `i` por una `t`:  
```csharp  
vocales[2] = 't';
Console.WriteLine(vocales[2]); // t
```  

#### Propiedades y métodos  
A pesar de que los arreglos implementan la interfaz `IList`, con todo y sus propiedades, la única rescatable para los arreglos unidimensionales es la propiedad `Length` (o `LongLength` si metemos más de 2^32-1 elementos en el arreglo), que nos devuelve la longitud del arreglo:
```csharp  
Console.WriteLine(vocales.Length); // 5

Console.WriteLine(conteo.Length); // 10

Console.WriteLine(misObjetos.Length); // 3
```  

##### Posdata
Sin importar su contenido, los arreglos siempre son tipos por referencia, por lo que es posible tener las siguientes líneas de código:
```csharp  
int [] arreglo = null;
double [] nulo = null;
```  
  
#### Lo que sigue
Inicialmente este post conendría todos los tipos de arreglos que permite crear C#, pero al final he decidido separarlos para hacerlo un poco más entendible, puedes visitar el siguiente sobre <a href="/post/arreglos-en-c-sharp-parte-2">arreglos multidimensionales en C#</a>.