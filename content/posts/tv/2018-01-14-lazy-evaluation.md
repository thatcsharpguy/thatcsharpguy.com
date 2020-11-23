---
title:  La evaluación perezosa
date:  2018-01-14 18:00:00
youtube:  QCN7ra82vqI
summary:  En programación la evaluación perezosa, es una técnica de evaluación que consiste en retrasar el cálculo (o ejecución) de una instrucción hasta que en realidad es necesaria.
featured_image:  featured.png
tweet_id:  952990497202692097
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

En programación la evaluación perezosa, o llamada por necesidad, o por su nombre en inglés: lazy evaluation. Es una técnica de evaluación, sí porque hay varias técnicas, que consiste en retrasar el cálculo (o ejecución) de una instrucción hasta que en realidad es necesaria.

Pero bueno, antes de entrar en detalles vamos a ver rápidamente otra estrategia, que podríamos definir como lo opuesto a la perezosa y esta es la evaluación temprana.

Este tipo de evaluación es la que normalmente usa tu lenguaje de programación, y por tanto creo que sin quererlo estás muy familiarizado con ella, ocurre cuando asignas una variable como un entero o una cadena:

```
var a = "Evaluación temprana"; // C#
a = "Evaluación temprana" # Python
String a = "Evaluación temprana"; // Java
```

Pero esto es demasiado simple, mejor vamos a ver algo más complejo. Imagina que vamos a trabajar con números del 0 al 10,000. Esta sería una forma de obtenerlos:

```
var numeros = Enumerable.Range(0, 10000).ToArray(); // C#
numeros = list(range(0, 10000)) # Python
int[] numeros = IntStream.range(0, 10000).toArray(); // Java 8
```

Es decir, el valor asociado con la variable es inmediatamente calculado (o evaluado) en el momento en el que se realiza la asignación, independientemente del momento en el que va a ser usado, si es que va a ser usado del todo. La sintaxis para crear estos arreglos es un poco rara, y es que los estamos forzando a ser evaluados tempranamente.

Por el contrario, y volviendo al tema principal de este video, en la evaluación perezosa los valores no son creados o calculados hasta que no se hace referencia a ellos después de declararlos. 

Un excelente ejemplo de la evaluación perezosa viene cuando estamos trabajando con colecciones de valores, imagina que quieres obtener una colección de valores... como por ejemplo, volviendo a los 0 al 10,000, esta es la forma de obtenerlos sin tener que calcularlos todos en el momento en el que lo definimos, es decir, utilizando la evaluación perezosa:  

```
var numeros = Enumerable.Range(0, 10000); // C#
numeros = range(0, 10000) # Python
int[] numeros = IntStream.range(0, 10000); // Java 8
```

Esto tiene varias ventajas como:

 - Incremento en el desempeño de la aplicación ya que se evita el hacer cálculos que no necesita
 - Posibilidad de escribir código "infinito" o sin límites de espacio puesto que los valores son generados "uno a la vez"  

Pero bueno, hasta ahora todo bien, pero cómo es que podemos crear nosotros código que se evalúe perezosamente. En concreto yo les voy a hablar de Python y C#, que básicamente utilizan la misma palabra reservada y esta es `yield`.

Con la palabra reservada `yield` se utiliza como la palabra `return` (y de hecho en C# se usan en conjunto) salvo que, a diferencia de usar return, `yield` se usa para indicar que nuestra función ha producido un resultado, pero... que no ha terminado de ejecutarse y que puede producir otro valor.

Toma por ejemplo este código (escrito en Python) que se usa para generar, teoricamente, una cantidad infinita de números de Fibonacci:

```
# https://stackoverflow.com/a/32930351/605482
# https://ideone.com/LE9Xsa

def getFibonacci():
    yield 0
    a, b = 0, 1
    while True:
        yield b
        b = a + b
        a = b - a
```

```
// https://stackoverflow.com/a/32930351/605482
// https://ideone.com/fBSdLy
IEnumerable<int> GetFibonacci()
{
    yield return 0;
    int a = 0, b = 1;
    while(true)
    {
        yield return b;
		    b = a + b;
		    a = b - a;
	    }
}
```

A pesar de que tiene un ciclo infinito ahí al momento de calcular el siguiente número, no significa que esté calculando todos desde un inicio, sino que los va calculando conforme nuestro código los va necesitando.

El tipo de retorno del método `getFibonacci` es un `generator` en lugar de una lista o un arreglo. Este `generator` es quien facilita la evaluación perezosa.

El código en C# es muy, muy similar, con las modificaciones apropiadas, y cambiando la palabra `yield` por `yield return` nuestro código realiza la misma tarea. Eso sí, a diferencia de Python, acá se regresa un tipo de dato llamado `IEnumerable`. Ambos, tanto `generator` como `IEnumerable` se comportan igual.

Una vez obtenido un `generator` o uno `IEnumerable` basta con usarlos dentro de un ciclo *foreach* o ir extrayendo elementos con la función `next` y el método `First`, respectivamente:

```
# https://ideone.com/LE9Xsa
fibonaccis = getFibonacci()
print("Primer número", next(fibonaccis))

for num in fibonaccis:
    if num > 30000:
        break
    print("Siguiente número", num)
```

```
// https://ideone.com/fBSdLy
var fibonaccis = GetFibonacci();
Console.WriteLine("Primer número " + fibonaccis.First());

foreach(var num in fibonaccis)
{
    if(num > 30000)
        break;
    Console.WriteLine("Siguiente número " + num);
}
```

Escribí un post sobre `yield` en C# por si es que les interesa conocer más a detalle. Así mismo, dejo enlaces a todo el código en la descripción. Ah, y por cierto, hay otro tipo de evaluación  llamada evaluación corto-circuitada... tiene un nombre peculiar pero no es nada complicada, escribí también un post sobre eso, dejo el link en la descripción.
