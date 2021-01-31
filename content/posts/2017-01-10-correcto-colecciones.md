---
layout: post
title: Colecciones en C#, ¿cuál elegir?
date: 2017-01-10 19:00:02
author: Antonio Feregrino
summary: La colección List&lt;T&gt; puede no siempre ser la adecuada para el trabajo que estás intentando hacer con tu aplicación, en este post te cuento sobre las otras colecciones que .NET nos ofrece.
featured_image: featured.jpg
images_folder: aprende-c-sharp/colecciones/
lang: es
tweet_id: 818954210729480192
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Hace poco escribí un <a href="..\yield-c-sharp">post acerca de la interfaz IEnumerable</a>, en el cual hablaba de su uso es para cuando trabajamos con colecciones. En fin, las colecciones son algo con lo que seguramente ya estás familiarizado, en particular con `List<T>`, pero hey, debes saber (ysi no es que ya lo sabes), que existen otro tipo de colecciones además de la ya vieja confiable `List<T>`. Y que pueden tener <a href="/tv/o-grande" target="_blank">un mejor desempeño</a>.

En este post voy a tratar de explicarte el uso básico de `Queue<T>`, `Stack<T>`, `HashSet<T>`, `Dictionary<TKey, TVal>`.

## List<T>  
Como dije antes, "la vieja confiable" y me atrevería a decir que es la colección más usada en los programas escritos con C#. Esto debido a que es, en teoría, la más sencilla de usar, ya que se comporta básicamente como los tan comunes arreglos al permitirnos **acceder a cualquier elemento** con la sintaxis de indizadores `[n]`, además da la posibilidad de **añadir elementos en cualquier posición** con `Insert` o emplear `Add` para **añadir elementos *al final* de la lista** sin necesidad de preocuparnos por reubicar los objetos ya existentes en ella:

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/colecciones/list.png" title="List" />

Esta es la colección *más* genérica y que nos puede ayudar en cualquiera de los casos, sin embargo no es la más reciomendable en cuanto a desempeño, y que si bien permite acceder a su contenido de forma constante *O(1)*, añadir nuevos elementos o insertar puede ser una operación muy pesada *O(n)*.

## Queue<T>  
En español la podríamos llamar "cola" o "fila", y se comporta como funcionan las filas o colas en la vida real: los nuevos elementos se forman "hasta atrás" y los viejos elementos salen "por delante". Para esta acción en lugar de contar con métodos como `Add` o `Insert`, nos ofrece `Enqueue` para **insertar elementos al final**, mientras que nos **permite conocer al elemento *de enfrente*** mediante `Peek` y **sacar dicho elemento** usando `Dequeue`:  

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/colecciones/queue.png" title="Queue" />

Esta colección es útil cuando no necesitamos acceso aleatorio a los datos o cuando es necesario mantener un orden determinado por el momento en que los elementos fueron añadidos a la colección. 

El ejemplo más claro es si estamos desarrollando una aplicación para asignar los clientes a las ventanillas de un banco, en donde el primero en llegar debe ser el primero en ser atendido.


## Stack<T>  
O como le decimos en español: la pila. En cierto sentido es muy similar a una Cola, en el sentido de que **permite insertar **al inicio*** usando `Push`, **acceder a él** usando `Peek` y **sacar el elemento más reciente** usando `Pop`: 

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/colecciones/stack.png" title="Stack" />

Esta colección tabmién es útil cuando no necesitamos acceso aleatorio a los datos y queremos tener o cuando es necesario obtener siempre el elemento más recientemente insertado. La pila es una estructura que se comoce como LIFO (*last in, first out*): el último en llegar es el primero en salir.

Las pilas son comunmente usadas para invertir conjuntos, o para implementar operaciones de hacer-deshacer, y para procesar operaciones matemáticas de notación infja o posfija.

## HashSet<T>  
La colección que a mi parecer es de las que menos se usan en el desarrollo, tiene una peculiaridad: no guarda los elementos dentro de ella, sino una representación numérica de ellos. Nos **ofrece un método para insertar** llamado `Add` que a su vez llama a `GetHashCode` para "convertir" lo que estamos insertando en un número entero y después guardarlo, y después revisar si existe meidante `Contains`:  

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/colecciones/hashset.png" title="HashSet" />

Esta es la colección ideal cuando únicamente queremos corroborar que un elemento existe dentro de un conjunto, insertar en un `HashSet` es una operación constante *O(1)*, al igual que revisar si alguno ya existe en él.

He visto muchas veces código como el siguiente:  

```csharp  
var descuentos = new List<double> { 10, 15, 30 };
var miDescuento = 5d;
// ...  

if(descuentos.Contains(miDescuento)) // ...
```  

Cuando bien podríamos obtener una mejora en el desempeño usando un `HashSet`, ya que no importa cuantos elementos contiene el set, obtendremos la respuesta *de inmediato*, mientras que si usamos una `List`, el tiempo que se tarda depende de cuantos elementos contenga:

```csharp  
var descuentos = new HashSet<double> { 10, 15, 30 };
if (descuentos.Contains(miDescuento)) // no hay necesidad de buscar
```  

## Dictionary<TKey, TVal>  
El diccionario almacena pares de valores: una llave y un objeto asociado. 
Una colección extremadamente similar a un `HashSet`, ya que de igual manera convierte uno de los elementos (la llave) a un entero para asociarlo con el objeto. **Para insertar ofrece el método** `Add`, además de que deja acceder a los valores mediante la sintaxis de indizador `[]`, y revisar si un valor o una llave existen en el diccionario usando `ContainsKey` y `ContainsValue`:

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/colecciones/dictionary.png" title="Dictionary" />

## Colecciones no genéricas  
Las colecciones no genéricas se comportan de manera similar, con la pequeña gran diferencia de que estas colecciones no tienen información de los objetos que almacenan. Para ellos son cualquier cosa... como si simplementa guardaran cajas sin conocer su contenido:  

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/colecciones/generics.png" title=""Genéricos"" />