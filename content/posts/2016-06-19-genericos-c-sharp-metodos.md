layout: post
title: Métodos genéricos en C#
date: 2016-06-19 19:00:01
author: Antonio Feregrino
excerpt: Los métodos genéricos proveen una funcionalidad similar a los tipos genéricos pero aplicada a métodos, también permiten la reutilización de código y reducen el impacto al desempeño en nuestras aplicaciones.
featured_image: generics-es.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/GenericMethods
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp

En un <a href="../genericos-c-sharp-clases">post anterior</a> hablé sobre las clases genéricas en C#, ahí mencioné que había otra posibilidad de usar el mismo concepto (de plantillas y marcadores) pero en la creación de métodos, y este post habla sobre ellos.

### Sintaxis

La sintaxis es casi la misma que al declarar un método tradicional, salvo que de nuevo aparecen los *placeholders* de tipos entre `<` `>`, por ejemplo, en el siguiente método se declara únicamente `T`:

```csharp  
static void WriteType<T>()
{
    var a = typeof(T);
    Console.WriteLine(a.Name);
}
```  

### Uso

Entonces, para llamarlo, tendremos que hacerlo de la siguiente manera:

```csharp  
WriteType<int>(); // Int32
WriteType<Program>(); // Program
```  

Al igual que con los tipos, una vez indicado el *placeholder* podemos utilizarlo tanto en la firma del método como en el cuerpo del mismo:

```csharp  
static T GetDefault<T>(T useless)
{
    return default(T);
}
```  

Para usarlos tenemos dos opciones:  
  
  1. Indicar explícitamente los tipos a la hora de realizar la llamada
  2. Permitir que el compilador infiera a qué tipos nos referimos a partir de los argumentos pasados

```csharp  
var dflt = GetDefault<int>(10);
var dflt2 = GetDefault(4.0); // Se infiere que es double a partir del argumento pasado al método
// int dflt3 = GetDefault(DateTime.Now); // Error, DateTime no se puede convertir a int implícitamente
```  

### Ejemplos  

Uno de los ejemplos más usados para explicar los métodos genéricos es un método que intercambia dos valores dados:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
static void Swap(ref decimal a, ref decimal b)
{
    decimal t = a;
    a = b;
    b = t;
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
static void Swap(ref int a, ref int b)
{
    int t = a;
    a = b;
    b = t;
}
```  
</div>  
</div>

Hasta aquí todo bien, ya podemos intercambiar de lugar decimales y enteros, pero ¿qué va a pasar cuando tengamos que intercambiar `DateTime` o `doubles`? podríamos copiar y pegar el código del método cambiando únicamente los tipos de dato... o podríamos en su lugar usar un método genérico:

```csharp  
static void Swap<T>(ref T a, ref T b)
{
    T t = a;
    a = b;
    b = t;
}
```  

Entonces ya podríamos usar el método con cualquier tipo que deseemos:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
int uno = 1, dos = 2;
Console.WriteLine(uno + " " + dos);

Swap<int>(ref uno, ref dos);
Console.WriteLine(uno + " " + dos);

Swap(ref uno, ref dos); // Se infiere int
Console.WriteLine(uno + " " + dos);
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
float d1 = 10, d2 = 20;
Console.WriteLine(d1 + " " + d2);

Swap(ref d1, ref d2); // Se infiere float
Console.WriteLine(d1 + " " + d2);  

  
  
```  
</div>  
</div>  

#### Errores

Cuando usamos métodos genéricos es posible incurrir en errores, los más comunes son que el compilador no pueda inferir los tipos a los que nos referimos, como en este caso que estamos usando `Swap` con un `int` y un `float`:   

```csharp  
Swap(uno, d2); // Error, los tipos no pueden ser inferidos
```  

O que intentemos usar un tipo de dato que no corresponde si especificamos explícitamente los tipos:

```csharp  
Swap<int>(ref uno, ref d2); // Error, d2 no es entero
```  

### Restricciones  

Al igual que los tipos genéricos, los métodos también aceptan restricciones usando la palabra reservada `where`, por ejemplo, en el método siguiente se restringe que tanto el tipo `T` como `W` sean `Athlete` o derivados de este. Además de que `T` tenga un constructor público sin parámetros:

```csharp  
static T ChangeSport<T, W>(W toCast)
    where T : Athlete, new()
    where W : Athlete
{
    // ...
```  

## Para cerrar  

Es importante señalar que un método de un tipo genérico no es genérico simplemente por eso, para que un método genérico sea considerado así debe tener al menos un *placeholder* de tipo. También debes considerar que a veces no todo es suceptible de *genericalizar*, es cuestión de analizar bien el problema.