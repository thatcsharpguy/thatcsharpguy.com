---
layout: post
title: Events en C#
date: 2016-08-22 19:00:01
author: Antonio Feregrino
summary: Un evento no es más que un mensaje que se envía de un objeto (el emisor) a otros (los receptores) cada vez que se cumple cierta condición o un cambio ocurre en él.
featured_image: events.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/events
tweet_id: 767867861721165825
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---


¿Recuerdas que en los primeros posts de este blog hablé sobre <a href="../delegados-en-c-sharp">delegados en C#</a>? pues bien, hoy toca hablar de un pariente muy cercano a ellos: Los eventos o `events`.  
  
El paso de mensajes entre objetos es uno de los pilares de la programación orientada a objetos, y un evento no es más que eso: un mensaje que se envía de un objeto (el emisor) a otros (los receptores) cada vez que se cumple cierta condición o un cambio ocurre en él.

Los receptores, por su cuenta, se suscriben a los eventos para ser informados cada vez que son *lanzados* desde el emisor y poder así manejarlos adecuadamente.

### Declarando eventos en el emisor

Para el código de ejemplo de este post cree una clase llamada `Generator`, que "genera" números aleatorios cada 0.5 segundos. Supongamos que queremos que la clase nos "avise" a través de un evento cada vez que esté a punto de generar un nuevo número. En C# declaramos eventos de la siguiente manera:

```csharp  
public delegate void GeneratingNumberEventHandler(Generator sender);

public event GeneratingNumberEventHandler GeneratingNumber;
```  

En la primera línea de código puedes observar a un viejo conocido, los delegados, en este caso es un delegado que regresa valor alguno y recibe una instancia de `Generator` como parámetro. Este delegado será el molde para nuestros manejadores de evento.

En la segunda línea se realiza la declaración del evento, la cual se compone de:  
  
 - El modificador de acceso (`public`, `internal`, ...).
 - La palabra reservada `event`.
 - El tipo del delegado asociado con ese evento.  
 - El nombre del evento.

### Manejando los eventos en el receptor  

Para que un receptor esté al pendiente de algún evento en un objeto debe suscribirse a él. La suscripción a los eventos se ejecuta con el operador `+=` más el **manejador de eventos**, que no es más que un método que cumple con los requisitos del evento:  

Por ejemplo, tenemos el siguiente método, que tiene `void` como valor de retorno y `Generator` como parámetro (y que por tanto puede ser usado como manjador para el evento declarado arriba):

```csharp  
static void G_GeneratingNumber(Generator sender)
{
    Console.WriteLine(sender.Name + " generará un número");
}
```  

Para asignarlo como manejador, debemos usar el operador `+=`:

```csharp  
g = new Generator(name);
g.GeneratingNumber += G_GeneratingNumber;
```  

### Invocando los eventos en el emisor

Así pues, dentro de nuestro método que genera los números podemos hacer algo como lo siguiente, justamente antes de que se genere un número: 

```csharp  
if (GeneratingNumber!= null)
{
    GeneratingNumber(this);
}
var generated = r.Next();
```  

Es importante que antes de invocar el evento compruebes que existe al menos un receptor (`GeneratingNumber!= null`) ya que de otro modo obtendrás una espantosa `NullReferenceException`.

Tras lo cual, si ejecutas el programa podrás ver algo como esto:  

<pre>
Locotrón generará un número  
Locotrón generará un número  
Locotrón generará un número  
</pre>

### Auxiliares (EventHandler y  y EventHandler<T>)  

De nueva cuenta, y al igual que con <a href="../func-y-action-en-c-sharp">Func y Action</a>, C# nos ofrece dos tipos auxiliares de delegado: `EventHandler` y `EventHandler<T>`, esto para no tener que definir delegados para cada evento en nuestro código. 

#### Declaración  

Para declararlo:

```csharp  
public event EventHandler GeneratingNumber1;
```  

#### Manejo  
El delegado `EventHandler` indica que el método devuelve `void` y recibe una instancia de `object` y una de `EventArgs` como parámetros... sí, un poco complicado, pero tiene sus razones. De nuevo, mira a la forma de asignar un manejador, en esta ocasión estamos <a href="../lambdas-en-c-sharp">usando una lambda</a>:  

```csharp  
g.GeneratingNumber1 += (sender, e) => 
{
    var generador = sender as Generator;
    Console.WriteLine(generador.Name + " generará un número (EventHandler)");
};
```  

Comúnmente el parámetro `sender` es una referencia al objeto que lanzó el evento, es por eso que se puede hacer el *cast* a `Generator` sin problema. 

#### Invocación  
La invocación no varía mucho, igual hay que checar que no sea nulo antes de llamar al `event`, solo que esta vez es necesario pasar dos argumentos:  

 - `this` que hace referencia a quién genera el evento, y
 - `EventArgs.Empty` 

```csharp  
if (GeneratingNumber1 != null)
{
    GeneratingNumber1(this, EventArgs.Empty);
}
```  

### Eventos con información extra (EventHandler<T>)  

Hay ocasiones en que los eventos por si mismos sean solo una parte de la información, por ejemplo: imagina que de nuestro generador de números aleatorios queremos saber cuándo se genera un número par, y saber cuál es el número par que se generó. Es ahí cuando podemos hacer uso de `EventHandler<T>`:


#### Declaración  

```csharp  
public event EventHandler<int> EvenNumberGenerated;
```  

#### Manejo  

```csharp  
g.EvenNumberGenerated += (sender, number) => 
{
    Console.WriteLine("Se generó el número par: " + number + " (manejador 1)");
};
```  

#### Múltiples manejadores  

Hay ocasiones en que para un mismo evento existen muchos suscriptores. Para suscribir más de un manejador de evento basta con seguir utilizando el operador `+=`

```csharp  
// Método para el manejador
static void G_EvenNumberGenerated(object sender, int number)
{
    Console.Write("Se generó el número par" + number + " (manejador 2)");
    if (number % 7 == 0)
    {
        Console.Write(" que además es múltiplo de 7, bye bye!");
        g.EvenNumberGenerated -= G_EvenNumberGenerated;
    }
    Console.WriteLine();
}

// Suscripción del manejador  
g.EvenNumberGenerated += G_EvenNumberGenerated;  
```  

#### Invocación  

Ahora, para invocar un evento de este tipo es necesario usar como argumentos el objeto que lo provoca y el valor asociado con el evento:   

```csharp  
var generated = r.Next();

if (generated % 2 == 0)
{
    if (EvenNumberGenerated != null)
    {
        EvenNumberGenerated(this, generated);
    }
    // ...
```  

Tras lo cual, el resultado de ejecutar el programa es el siguiente:  

<pre>
Leña generará un número
Leña generará un número (EventHandler)
Se generó el número par: 1817673842 (manejador 1)
Se generó el número par1817673842 (manejador 2)
Leña generará un número
Leña generará un número (EventHandler)
Se generó el número par: 1464150850 (manejador 1)
Se generó el número par1464150850 (manejador 2)
</pre>

### Desuscribiéndose de eventos  

Así como podemos suscribirnos a eventos, también es posible hacer lo contrario: desuscribirse de los eventos. Nos desuscribimos de un evento cuando no queremos seguir al tanto de su invocación.  

Para desuscribimos usamos el operador `-=` junto con el manejador de evento con el que nos suscribimos:  

```csharp  
g.EvenNumberGenerated -= G_EvenNumberGenerated;
```  

Si en tu código tendrás que desuscribir eventos, lo ideal es que los declares como métodos con nombre, no lambdas y no delegados, ya que es más natural <a href="http://stackoverflow.com/questions/183367/unsubscribe-anonymous-method-in-c-sharp" target="_blank">remover un manejador</a> que uno anónimo.  
  
### ¿Por qué no usar un delegado?  

Una de las preguntas que te podrían surgir es ¿por qué no usar solamente un `delegate` en lugar de la sintaxis extra de los `event`?

La respuesta es un tanto sencilla, además del valor semántico que le da un evento a tu código, los eventos tienen dos características:  

 - Solamente pueden ser invocados dentro del objeto al que pertenecen.    
 - Únicamente pueden ser eliminados (igualados a `null`) dentro del objeto al que pertenecen.  

 O, en código:

```csharp  
// Dentro de la clase Generator
public Action<int> EvenNumberGeneratedAction;

//  Fuera de la clase Generator			
//g.EvenNumberGenerated(5); // No se puede ejecutar el evento desde "afuera"
g.EvenNumberGeneratedAction(3);

//g.EvenNumberGenerated = null; // No se puede "eliminar" a todos los manejadores desde fuera  
g.EvenNumberGeneratedAction = null;
```  

### Conclusión  

Los eventos son de mucha importancia cuando se programan aplicaciones en C#, y más aún cuando se tiene que reaccionar a cambios en un objeto o cuando debemos estar al tanto de interacciones con los usuarios. Conocer qué es lo que tienen detrás y cómo es que se declaran, invocan y manejan debería estar en el repertorio de todo desarrollador en C#. 