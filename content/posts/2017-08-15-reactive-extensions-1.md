---
layout: post
title: Programación reactiva y Rx, parte 1
date: 2017-08-15 19:00:01
author: Antonio Feregrino
summary: La programación reactiva es una de las formas de implementar el patrón del observador en nuestro código, y las Reactive Extensions hacen esta tarea mucho más sencilla.
featured_image: plain.png
images_folder: /rx/
github: https://github.com/ThatCSharpGuy/rx
lang: es
tags: Rx, ReactiveExtensions
featured_tag: Rx
---

La programación reactiva es un paradigma de programación que está ganando mucha popularidad últimamente, esto se le puede atribuir a la funcionalidad que nos ofrece y a la simpleza con la que se puede llegar a leer el código. 

Es una de las maneras de implementar el <a href="https://youtu.be/NmuRwZ2CYvA" target="_blank"><i>patrón del observador</i></a> descrito por *The Gang of Four* en su famoso libro. Este patrón nos ayuda para establecer un medio de comunicación unilateral entre dos objetos en la que uno, conocido como el publicador, es el que envía mensajes y otro, conocido como el suscriptor, los recibe. A este conjunto de mensajes enviados en una sola dirección lo conoceremos de ahora en adelante como  *flujo de datos*. 


### En .NET  
En .NET te encontrarás con dos interfaces que hacen las veces de este par de actores de los que habla el patrón del observador: El publicador es del tipo `IObservable<T>` mientras que el observador es `IObserver<T>` en donde `T` es el mensaje que se enviará entre ellos.  

### `IObservable<T>` (el publicador)  
Esta interfaz únicamente especifica un método `Suscribe` para añadir suscriptores a sus flujos de datos

### `IObserver<T>` (el suscriptor)
A diferencia del publicador, el suscriptor debe implementar más métodos para reaccionar de acuerdo a lo que el publicador envíe a través del canal de comunicación. La interfaz especifica tres métodos:  

- **`OnNext`**, que será llamado cada vez que el publicador envíe un nuevo mensaje.
- **`OnCompleted`**, que será llamado una vez que el suscriptor de por terminada la comunicación.
- **`OnError`**, que será llamado si es que dentro del publicador ocurre <a href="..\excepciones-c-sharp" target="_blank">un error</a>.  

### Reactive Extensions (Rx)  
Las interfaces mencionadas anteriormente están disponibles en el *framework* desde la versión 4, sin embargo, para sacarle todo el jugo a la programación reactiva (sin tener que reinventar la rueda nosotros mismos), también es necesario instalar una biblioteca desde NuGet, así que instala el paquete: 

```  
PM> Install-Package System.Reactive
```  

En todos los lugares en los que vayas a hacer uso de este paradigma de programación. Por cierto, `System.Reactive` antes era `Rx-Main` por si llegaran a encontrar algún tutorial que hace referencia a este paquete, no se rompan la cabeza tratando de encontrarlo.  

## Algo no reactivo  
Para comenzar vamos a ver un ejemplo de algo que no es reactivo. Comencemos por un arreglo de datos enteros:  

```csharp  
var datos = new[] { 3, 4, 6, 8, 11, 13, 15, 15, 13, 10, 6, 4 };
```  

Y luego una forma de **tomarlos** uno a uno e imprimirlos a consola:  

```csharp  
foreach (var dato in datos)
    Console.Write($"{dato}, ");
Console.WriteLine("\nTerminé de leer los datos");
```  

Al ejecutarlo obtendremos algo como esto:

<pre>
3, 4, 6, 8, 11, 13, 15, 15, 13, 10, 6, 4, 
Terminé de leer los datos
</pre>

Nada raro: tenemos un arreglo y **sacamos** los valores de él para imprimirlos a consola. Ahora vamos a ver cómo haríamos esto con programación reactiva:  

## Algo reactivo  
Comenzaremos por convertir nuestros `datos` a un flujo de datos, para esto nos ayudaremos <a href="..\extension-methods-es" target="_blank">método de extensión</a> `ToObservable` que tomará un `IEnumerable` y lo convierte en un observable (o publicador):  

```csharp  
IObservable<int> flujoDatos = datos.ToObservable();
```  

Como puedes ver, nuestro objeto `flujoDatos` es ahora un `IObservable<int>` y para recibir información de él, ahora también sabes que debemos suscribirnos a él mediante un `IObserver<int>`, ¿cierto?

Pues verás, otra de las ventajas de usar las **Reactive Extensions** es que podemos suscribirnos usando `Suscribe` especificando únicamente las acciones que vamos a realizar cada vez que se llame a `OnNext`, `OnCompleted` y `OnError`. Esto mediante el uso de <a href="..\delegados" target="_blank">expresiones lambda</a>:

```csharp  
flujoDatos.Subscribe(
    onNext: dato => { Console.Write($"{dato}, "); },
    onCompleted: () => { Console.WriteLine("\nTerminé de recibir los datos"); }
);
```  

Al ejecutar el código obtendremos algo así en pantalla:  

```csharp  
3, 4, 6, 8, 11, 13, 15, 15, 13, 10, 6, 4, 
Terminé de recibir los datos
```  

Pareciera que nada cambió, sin embargo, la diferencia está en la forma en la que los números llegaron al método `Console.Write`:

## La diferencia  
La diferencia radica en que usando la primera forma (la no reactiva), en el código le estamos dando dos órdenes al programa:

1. Sacar los datos del arreglo
1. Una vez que tenga el dato, imprimirlo en pantalla

Este modo de programación se le conoce como **programación imperativa**.

Mientras que del segundo modo, nosotros únicamente le estamos asignando una sola tarea al programa:  

1. Una vez que tenga el dato, imprimirlo a pantalla 

En este caso no es necesario que le ordenemos sacar los datos del flujo de datos, estos llegarán solos al *observador*, lo único que tenemos que hacer es decirle cómo reaccionar cuando recibe información. Este modo de programación se conoce como **programación declarativa**.  

### Pull vs. Push  
Una forma de visualizar esta diferencia puede ser con las notificaciones que recibes en tu teléfono móvil: cada vez que alguien te menciona en un tweet o te llega un correo electrónico, tu teléfono recibe una notificación *push* del servidor, a la cual debe reaccionar de determinada manera. Mientras que si tu entras a la app del correo o a la app de Twitter y refrescas la aplicación eres tú mismo quien está solicitando (mediante un *pull*) la información.  

Pero bueno, ese fue un ejemplo muy, muy básico de las *Reactive Extensions*, ¿vemos otro un poco más elaborado?  

## No solo enteros  
En este caso estamos utilizando un flujo de `int`s, pero la programación reactiva tiene muchos usos: imagina una aplicación para escritorio. Podrías visualizar a los clicks que el usuario hace en ella como un flujo, un flujo de clicks. Y hacer que la app **reaccione** a ellos mediante este modo de programación.

## LINQ y Rx  
Empaquetadas junto con las *Reactive Extensions*, vienen otros métodos de extensión que permiten aplicar muchos de las operaciones de Linq sobre los `IObservables` como si de secuencias tradicionales se tratara, entonces podemos filtrar con `Where`:

```csharp  
flujoDatos
    .Where(dato => dato % 3 == 0)
    .Subscribe(
        onNext: dato => { Console.Write($"{dato}, "); },
        onCompleted: () => { Console.WriteLine("\nTerminé de recibir los múltiplos de 3"); }
    );
```  

<pre>
3, 6, 15, 15, 6, 
Terminé de recibir los múltiplos de 3
</pre>

Saltarnos algunos valores con `Take` y `Skip`:

```csharp  
Console.WriteLine();
flujoDatos
    .Skip(2)
    .Take(3)
    .Subscribe(
        onNext: dato => { Console.Write($"{dato}, "); },
        onCompleted: () => { Console.WriteLine("\nTerminé de recibir los tres elementos a partir del 2do"); }
    );
```  

<pre>
6, 8, 11, 
Terminé de recibir los tres elementos a partir del 2do
</pre>

Y usar funciones de agregación como `Min`

```csharp  
Console.WriteLine();
flujoDatos
    .Min()
    .Subscribe(
        onNext: dato => { Console.Write($"{dato}, "); },
        onCompleted: () => { Console.WriteLine("\nTerminé de recibir los datos y presenté el menor"); }
    );
```  

<pre>
3, 
Terminé de recibir los datos y presenté el menor
</pre>

## Concurrencia y Rx  
Otra de las ventajas que nos ofrece la programación reactiva es la asincronía bajo la que está diseñado para operar. Al ser reactiva permite que las acciones del programa no tengan que seguir una secuencia y los mensajes se envíen/reciban en cualquier momento. También, usando otros métodos de extensión como `SuscribeOn` y `ObserveOn` se puede delegar la ejecución de los `IObservers` a otros hilos distintos de los que fue llamado.

## Hagamos todo reactivo... ¿o no?  
Espero haberte dado un pequeño empujón hacia la programación reactiva, pero ahora para completar el trabajo es importante que te diga que no significa que vayas por el código de tus apps convirtiendo todos los `IEnumerable` a flujos de dato o que busques la manera de convertir todos tus eventos a `IObservables`, no. Considera usar este paradigma para los casos en los que:

- Tengas que lidiar con eventos de interfaz gráfica como un usuario dando clicks o moviendo *sliders*  
- Estés trabajando con información en tiempo real
- Estés trabajando con flujos de datos  

Es totalmente entendible si te quedas con dudas, recuerda que me puedes mandar un tweet o un correo electrónico, además puedes  ver el <a href=" https://github.com/ThatCSharpGuy/rx" target="_blank">código de este post en GitHub</a>.

Definitivamente Rx es un tema que da para más y sin duda <a href="..\reactive-extensions-2" target="_blank">estaré escribiendo sobre él</a> en el futuro.