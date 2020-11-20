layout: post
title: out y ref en C#
date: 2016-04-11 19:00:01
author: Antonio Feregrino
excerpt: Seguramente si en alguna vez de tu carrera has usado código C o C++ te has encontrado con estos operadores&#58; &#42; y &amp; que, en pocas palabras, nos sirven para trabajar con punteros y también sabrás que estos nos permitían pasar como referencia una variable.
featured_image: ref-out.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/ref-out
tweet_id: 719643375478054913
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp

En un post anterior hablé acerca de <a href="../metodos-c-sharp">métodos en C#</a>, ahí mencioné que los parámetros que recibía un método nos daban muchas posibilidades a la hora de desarrollar, y la característica de la que hablaré es otra de esas posibilidades.

Seguramente si en alguna vez de tu carrera has usado código C o C++ te has encontrado con estos operadores: `*` o  `&` que, en pocas palabras, nos sirven para trabajar con punteros y también sabrás que estos nos permitían pasar como referencia una variable, entonces ya sabíamos que para modificar el valor de una variable dentro de una función teníamos que pasar el apuntador a dicho objeto como argumento.

Pues bien, en C# existen las palabras `ref` y `out` que vienen a realizar una tarea similar ya que al usarlos para declarar y llamar a algún método, permiten que el argumento sea pasado por referencia (digamos que algo "similar" a los punteros) y no por valor.

### Declaración

Si deseamos emplear valores por referencia en algún método, es necesario expresarlo desde su definción:

```csharp  
public void Swap(ref int a, ref int b) // ...
public bool SumaMaxima(int a, int b, int max, out int resultado) // ...
public string WeirdMethod(ref int a, ref int b, out int s, out int r) // ...
```  

En el primer caso, vemos que tanto `a` como `b` son enteros que el método recibe por referencia. En el segundo, únicamente `resultado` es una referencia y en el tercero `a`, `b`, `s` y `r` son pasados de esta manera.

Para trabajar con las referencias dentro del método no tenemos que hacer nada especial, podemos usarlas como si fueran parámetros normales:

```csharp  
public void Swap(ref int a, ref int b)
{
    int m = a;
    a = b;
    b = m;
}

public bool SumaMaxima(int a, int b, int max, out int resultado)
{
    resultado = a + b;
    if (resultado >= max)
    {
        resultado = max;
        return true;
    }
    return false;
}

public string WeirdMethod(ref int a, ref int b, out int s, out int r)
{
    s = a + b;
    r = a - b;
    return a.ToString() + b.ToString();
}
```  

### Uso  

Para usarlos, también es necesario indicar en la llamada al método que los parámetros deben ser pasados de determinado modo, fíjate en la llamada a `Swap` en el siguiente código

```csharp  
int a = 100, b = 5;
Console.WriteLine("Valor de a:" + a + " - Valor de b:" + b);

parametros.Swap(ref a, ref b);

Console.WriteLine("Valor de a:" + a + " - Valor de b:" + b);
```  

El resultado es el siguiente

<pre>
Valor de a:100 - Valor de b:5  
Valor de a:5 - Valor de b:100
</pre>

Y esta es una llamada a un método con `out`

```csharp  
int resSuma;
var isResultOk = parametros.SumaMaxima(a, b, 100, out resSuma);

if (isResultOk)
    Console.WriteLine("La suma excedió el valor máximo");
else
    Console.WriteLine("El resultado de la suma es " + resSuma);
```  

Que da como resultado
```
La suma excedió el valor máximo
```

### La diferencia entre ref y out

Hasta ahora parecen similares, sin embargo, mientras que ambas son referencias, la semántica que tienen es distinta.

+*Debemos usar `out` cuando se espera que sea el método llamado el encargado de asignarle valor**, así que si creas un método con un parámetro del tipo `out` y no le asignas valor dentro del mismo, el compilador te dará un error. Por ejemplo, si la definición de `WeirdMethod` fuera la siguiente:

```csharp  
public string WeirdMethod(ref int a, ref int b, out int s, out int r)
{
    return a.ToString() + b.ToString();
}
```  

Obtendríamos un error como este: `The out parameter 'r' must be assigned to before control leaves the current method`.

Además, **la variable que le pasamos como `out` puede o no estar inicializada antes de ser enviada al método** sin que el compilador genere siquiera una advertencia.

### Ejemplo de la vida real
Existen muchos escenarios en los que podemos usar el paso por referencia en nuestros programas, pero uno de los ejemplos màs significativos son los métodos `TryParse`, que reciben un parámetro `out` y regresan un valor `bool`. En ellos se intenta hacer la conversión de cadena al tipo de dato especificado y devuelven verdadero o falso dependiendo de si la conversión resultó exitosa, y en caso de que si, el valor vendrá asignada en el parámetro marcado:

```csharp  
string dec = "abcd";
string dec2 = "555";

decimal one;
decimal two;

if (Decimal.TryParse(dec, out one))
    Console.WriteLine("Convertí " + dec + " en " + one);
else
    Console.WriteLine(dec  + " no es convertible a decimal");


if (Decimal.TryParse(dec2, out two))
    Console.WriteLine("Convertí " + dec2 + " en " + two);
else
    Console.WriteLine(dec  + " no es convertible a decimal");
```  

### Lo que sigue

Te invito a que pruebes con el código que acompaña al post y que si tienes alguna duda me la hagas saber en Twitter o al correo electrónico. En realidad no es muy común que tu tengas que implementar los parámetos de este tipo en tu código, sin embargo si se puede presentar el caso y no hay más que estar preparado y conocerlos.