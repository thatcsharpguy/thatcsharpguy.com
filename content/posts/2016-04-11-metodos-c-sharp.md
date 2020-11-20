---
layout: post
title: Métodos en C#
date: 2016-04-11 19:00:00
author: Antonio Feregrino
summary: Un método no es más que un procedimiento o acción asociado con una clase y se compone de datos y un comportamiento. Esta es la manera de definir un método en C#.
featured_image: metodos.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Methods
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Un sistema robusto programado en un lenguaje de programación orientado a objetos, como es el caso de C#, debe estar separado en distintas unidades lógicas. Estas unidades lógicas suelen coordinarse mediante el envío de mensajes entre ellos, en C# dichos mensajes se llaman métodos.

### Definición

**Un método no es más que un procedimiento o acción asociado con una clase** y se compone de datos y un comportamiento. Esta es la sintaxis común para escribir un método:

```
[modificador de acceso] <tipo de retorno> <nombre>([parámetros])  
{
    [implementación]  
}
```

Las partes marcadas con `[]` son opcionales y las que están entre `<>` son necesarias:

<ol>
<li><strong>modificador de acceso</strong>, es una palabra reservada que especifica cuales de los otros objetos tendrán acceso al método, puede ser:
        <ul>
            <li><code>public</code>. Todos tienen acceso.</li>
            <li><code>protected</code>. Solo las clases derivadas tienen acceso.</li>
            <li><code>internal</code>. Solo clases contenidas en el mismo ensamblado tienen acceso.</li>
            <li><code>private</code>. Solo la clase que lo define tiene acceso él.</li>
        </ul>
Además, también podemos añadir el modificador <code>static</code> para especificar que un método debe ser llamado desde una instancia de la clase o desde la clase misma.
<br />
Si se omite, el valor por default es <code>private</code>.
</li>
<li><strong>tipo de retorno</strong>, se usa para declarar si el método retornará algo como parte de su ejecución. Los valores posibles pueden ser muchos, y dependen de la lógica del negocio. Un método puede regresar <a href="http://thatcsharpguy.com/post/tipos-dato-c-sharp">tipos por valor o referencia</a>, pero solo puede retornar un solo tipo.
<br />
En caso de que no exista necesidad de regresar algún valor, debemos escribir <code>void</code>.
</li>
<li><strong>nombre</strong>, esta es una parte esencial de la definición de un método. El nombre es un identificador que no debe comenzar por un número ni contener caracteres que no sean alfanuméricos o guiones bajos.
<br />
Por convención, en C# los nombres de método comienzan con mayúscula, aunque tu puedes escribirlos como quieras. 
</li>
<li><strong>lista de parámetros</strong>, los métodos pueden recibir información del objeto que los llama a través de los parámetros, la lista de parámetros está definida por el método mediante la especificación del tipo de dato y el identificador de este, si es necesario que el método reciba más de un parámetro estos se separan por una coma.</li>
<li><strong>implementación</strong>, es un conjunto de <a href="http://thatcsharpguy.com/post/sentencias-expresiones-operadores">sentencias</a> que definen el comportamiento del método.</li>
</ol>

### Ejemplos  
Uno de los más grandes ejemplos de métodos es el método `Main`, que es el punto de entrada de cualquier programa escrito en este lenguaje. Por ejemplo:

#### Main:

```csharp  
public static void Main(string[] args) ...
```  

Que especifica que:  

 - el método `Main` es público
 - no necesitamos instanciar la clase para llamarlo
 - no regresa ningún valor
 - recibe un arreglo de cadenas como argumento
 
#### GetDefaultCar;

```csharp  
internal static F1Car GetDefaultCar()
{
    return new F1Car();
}
```  

Que especifica que:  

 - el método `GetDefaultCar` únicamente podrá ser usado dentro del ensamblado en donde se encuentra la calse
 - como es estático, no necesitamos instanciar la clase para llamarlo
 - regresa un objeto del tipo `F1Car`
 - no recibe parámetros
 
#### ChangeDriver: 
 
```csharp  
public void ChangeDriver(Pilot newPilot)
{
    if (!newPilot.Equals(_currentPilot))
    {
        _currentPilot = newPilot;
    }
}
```  

Que especifica que:  

 - el método `ChangeDriver` es público
 - necesitamos instanciar la clase para llamarlo
 - no regresa ningún valor
 - recibe un parámetro de tipo `Pilot` llamado `newPilot`
 
#### TryStartEngine: 
 
```csharp  
public bool TryStartEngine()
{
    if (_currentPilot == null)
    {
        return false;
    }
    StartEngine();
    return true;
}
```  

Que especifica que:  

 - el método `TryStartEngine` es público
 - necesitamos instanciar la clase para llamarlo
 - no regresa un valor booleano
 - no recibe parámetros
 
#### StartEngine:

```csharp  
void StartEngine() 
{
    IsRunning = true;
}
```  

Que especifica que:  

 - el método `StartEngine` es privado, ya que al no tener modificador de acceso por default es `private`
 - necesitamos instanciar la clase para llamarlo
 - no regresa ningún valor
 - no recibe parámetros
 
### Uso
De entrada, el método `Main` es reconocidísimo, ya que es el punto de entrada de cualquier aplicación en C#, a continuación, obtenemos un objeto `F1Car` usando el método de instancia:

```csharp  
var car = F1Car.GetDefaultCar();
```  

Podemos colocarle un piloto usando el método público:

```csharp  
car.ChangeDriver(schumacher);
```  

Ahora con piloto, podríamos encender el motor ¿no?

```csharp  
//car.StartEngine();
var started = car.TryStartEngine();
```  

No podemos llamar directamente a `StartEngine` ya que es privado, pero si tenemos acceso a `TryStartEngine`, desde el cual si se puede acceder a los miembros privados de la clase.

### Conclusión  
En mi opinión la forma de definir y usar los métodos en C# es una de las más sencillas que existen actualmente, esto debido a que se usan palabras muy descriptivas para esta tarea. Además la lista de parámetros es claramente definida desde un inicio.

### Lo que sigue
Aún queda mucho qué hablar sobre los métodos, por ejemplo, ¿qué son los métodos de extensión? y hablar de los modos en los que los parámetros pueden ser pasados, y es que estos pueden ser pasados por referencia o por valor, podemos usar parámetros por default y los llamados *named parameters*, expandiendo las posibilidades a la hora de programar.