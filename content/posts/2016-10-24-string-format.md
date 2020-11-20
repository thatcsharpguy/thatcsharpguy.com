---
layout: post
title: String.Format
date: 2016-10-24 19:00:01
author: Antonio Feregrino
excerpt: Olvídate de concatenar una interminable cantidad de cadenas para presentar la información en tus aplicaciones. Con este método de la clase String tienes (casi) todo resuelto, únicamente necesitas una cadena con marcadores y listo.
featured_image: format.jpg
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/StringFormat
lang: es
tweet_id: 795799488346648576
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

El método estático `Format` de la clase `String` nos ayuda en el manejo de las cadenas en C#, específicamente a la hora de la presentación de la información. Antes de continuar, es importante que conozcas la firma del método que estamos a punto de discutir:  

```csharp  
public static string Format (string format, params object[] args);
```  

Lo cual significa que recibe una cadena y una colección de objetos y devuelve una cadena. Ahora sí, mejor vemos su funcionamiento, toma por ejemplo la siguiente línea de código:  

```csharp  
Console.WriteLine(String.Format("Buen día señor {0}", "sol"));
```  

Que produce el siguiente resultado en la pantalla:  ```Buen día señor sol```.  

Wow, ¿viste lo que ocurrió? ... La cadena "{0}" fue reemplazada por "sol". Esa es básicamente la función de `Format`: **reemplazar cadenas por valores**. Pero las cadenas no tienen que ser cualquier cosa, sino una cadena "especial", que debe contener marcadores (o *placeholders*) que se forman de un índice entero rodeado de las llaves `{` y `}`.  

El índice indica en qué lugar dentro de la función aparecerá el valor por el cual debe ser reemplazado, en el caso del código anterior `"sol"` aparece en el lugar 0 (contando de izquierda a derecha) a partir de que termina la cadena "original". En caso de que hubiera más de un parámetro podríamos usar `{1}`, `{2}` y así sucesivamente:

```csharp  
Console.WriteLine(String.Format("{0}, {1} y {2}", "uno", "dos", "tres")); // Imprime: "uno, dos y tres"
Console.WriteLine(String.Format("{1}, {0} y {2}", "uno", "dos", "tres")); // Imprime: "dos, uno y tres"
Console.WriteLine(String.Format("{2}, {0} y {1}", "uno", "dos", "tres")); // Imprime: "tres, uno y dos"
```  

Del código anterior observa cómo a pesar de que parámetros aparecen en el mismo orden, cambia la forma en que aparecen en la pantalla.

## Formato especial  
Además de decidir la ubicación de nuestros objetos en la cadena formateada con las llaves e índices (`{0}`,`{1}`, ...), también podemos darles un formato más específico. Esto se logra modificando un poco el marcador que indica su posición, la modificación consiste en agregar dos puntos a la derecha del índice, seguido del formato que deseamos darle, todo esto dentro de las llaves. Es decir, `{0}` se transformaría en `{0:X}`, o `{2}` se transformaría en `{2:#,##0.00}`, la idea se entiende. De los formatos hablaremos un poco más adelante. 

## Formateando enteros
Formatear enteros es probablemente algo que tendrás que hacer comúnmente, así que por qué no aprender algunas cuantas cosas para hacer que luzcan mejor. Para darle formato a los enteros usando `Format` tenemos varias opciones a nuestro alcance. Por ejemplo, podemos hacer que se el número tenga tantos ceros a la izquierda como deseamos (cinco ceros en este caso): 

```csharp  
Console.WriteLine(String.Format("{0:00000}", 1300)); // Imprime: "01300"
Console.WriteLine(String.Format("{0:00000}", -52)); // Imprime: "-00052"
Console.WriteLine(String.Format("{0:00000}", 0)); // Imprime: "00000"
```  

O, ¿quién no conoce la notación exponencial? pues también la podemos usar para formatear nuestros números, el modificador de formato es la letra `e`: 

```csharp  
Console.WriteLine(String.Format("{0:e}", 1300)); // Imprime: "1.300000e+003"
Console.WriteLine(String.Format("{0:e}", -52)); // Imprime: "-5.200000e+001"
```  

El modificador de formato `c` puede resultar muy útil para aplicaciones que manejan cantidades monetarias, ya que se encarga de darles formato y añadirle el signo de la moneda sin necesidad de más esfuerzo del programador:

**NOTA:** Ten especial cuidado con este modificador de formato, ya que modificará el número de acuerdo a la cultura de tu aplicación. 

```csharp  
Console.WriteLine(String.Format("{0:c}", 1300)); // Imprime: "$1,300.00
Console.WriteLine(String.Format("{0:c}", -52)); // Imprime: "($52.00)"
Console.WriteLine(String.Format("{0:c}", 0)); // Imprime: "$0.00"
```  

Y no podríamos dejar de lado a los números hexadecimales, su modificador es la letra `x` seguido de un número entero que indica el número de posiciones que debe ocupar, mira:

```csharp  
int r = 0, g = 133, b = 20;
Console.WriteLine(String.Format("Hex: R{0:x}, G{1:x4}, B{2:x2}", r, g, b)); // Imprime: "Hex: R0, G0085, B14"
Console.WriteLine("Hex: {0:x2}{1:x2}{2:x2}", r, g, b); // Imprime: "Hex: 008514"
```  

## Formateando numeros reales  
Al igual que los enteros, para los números reales (`double`, `decimal` y `float`) también se pueden formatear. Comenzando por el modificador `r` que supuestamente garantiza que un número será convertido a cadena y que posteriormente podremos *parsear* esa cadena de vuelta al número y obtener el mismo valor, aunque esto <a href="http://stackoverflow.com/questions/24299692/why-is-a-round-trip-conversion-via-a-string-not-safe-for-a-double?rq=1" target="_blank">no es del todo cierto</a>. Generalmente se usa `r` para obtener una representación exacta del número:  

**NOTA:** Este modificador de formato únicamente puede ser usado con números `double` y `float`. 

```csharp  
Console.WriteLine(String.Format("{0:r}", 0.6852871999174d)); // Imprime: "0.6852871999174"
Console.WriteLine(String.Format("{0:r}", 0.6852871999174f));  // Imprime: "0.6852872"
Console.WriteLine(String.Format("{0:r}", 0.6852871999174m));  // Error, r no está disponible para decimal
```  
También podemos especificar cuántos decimales deben aparecer cuando imprimimos el número, usando `0`s para indicar la cantidad de decimales: 

**NOTA:** Este modificador de formato redondeará los números a su valor más cercano. 

```csharp  
Console.WriteLine(String.Format("{0:#,##0.00}", 13000d)); // Imprime: "13,000.00"
Console.WriteLine(String.Format("{0:#,##0.00}", 0.6852871999174f));  // Imprime: "0.69"
Console.WriteLine(String.Format("{0:#,##0.00}", -0.50m));  // Imprime: "-0.50"
```  

Si por otro lado estamos trabajando con números que representan porcentajes, podemos hacer uso del modificador `0%`, este modificador tomará el valor y lo multiplicará por 100 e imprimirá el resultado acompañado del símbolo de por ciento:  

```csharp  
Console.WriteLine(String.Format("{0:0.0%}", 13000d)); // Imprime: "1300000.0%"
Console.WriteLine(String.Format("{0:0.0%}", 0.6852871999174f));  // Imprime: "68.5%"
Console.WriteLine(String.Format("{0:0.0%}", -0.50m));  // Imprime: "-50.0%"
```  

El resto de modificadores de formato los puedes encontrar en <a href="https://msdn.microsoft.com/en-us/library/0c899ak8(v=vs.110).aspx" target="_blank">este enlace</a>.    

## Formato condicional
Para los tipos numéricos, `String.Format` tiene una opción más de formateo, que es útil si quieres hacer distinción entre números positivos, negativos y con valor 0. La sintaxis no es tan distinta a las anteriores, basta con separar con `;` los formatos:  

```  
"{0:[formato si positivo];[formato si negativo];[formato si cero]}"  
```  

Los usos de este pueden ser muy variados, por ejemplo, imagina que un requerimiento de tu aplicación es poner guiones en lugar de 0 cuando muestres cifras y la palabra MENOS cuando se trata de números negativos:  

```csharp  
Console.WriteLine(String.Format("{0:#,##0.00;MENOS #,##0.00;—}", 13000)); // Imprime: "13,000.00"
Console.WriteLine(String.Format("{0:#,##0.00;MENOS #,##0.00;—}", 0));  // Imprime: "—"
Console.WriteLine(String.Format("{0:#,##0.00;MENOS #,##0.00;—}", -0.50m));  // Imprime: "MENOS 0.50"
```  

Otro posible uso es para esos sistemas, raros, en donde se utilizan los valores 1 y -1 como booleanos:  

```csharp  
Console.WriteLine(String.Format("{0:VERDADERO;FALSO;DESCONOCIDO}", 1)); // Imprime: "VERDADERO"
Console.WriteLine(String.Format("{0:VERDADERO;FALSO;DESCONOCIDO}", 0));  // Imprime: "DESCONOCIDO"
Console.WriteLine(String.Format("{0:VERDADERO;FALSO;DESCONOCIDO}", -1));  // Imprime: "FALSO"
```  

También puedes hacer cosas como mostrar si un balance es positivo o negativo sin mostrar los números y sin la necesidad de agregar sentencias de control a tu código. Deja que `Format` haga el trabajo.  

## Formateando fechas  
Probablemente las fechas sean el tipo de dato que más opciones de formato posean. Toma por ejemplo las siguientes opciones de formato:  
 
```csharp  
var now = DateTime.Now;
Console.WriteLine(String.Format("{0:d}", now)); // Imprime: "22/10/2016"
Console.WriteLine(String.Format("{0:F}", now)); // Imprime: "sábado, 22 de octubre de 2016 09:54:33 a.m."
Console.WriteLine(String.Format("{0:M}", now)); // Imprime: "22 de octubre"
Console.WriteLine(String.Format("{0:T}", now)); // Imprime: "09:54:33 a.m."
Console.WriteLine(String.Format("{0:r}", now)); // Imprime: "Sat, 22 Oct 2016 09:54:33 GMT"
```  

Pero también puedes crear tus formatos propios, por ejemplo:  
 
```csharp  
var now = DateTime.Now;
Console.WriteLine(String.Format("{0:dd/MM/yyyy}", now)); // Imprime: "24/10/2016"
Console.WriteLine(String.Format("{0:dd-MM-yyyy}", now)); // Imprime: "24-10-2016"
Console.WriteLine(String.Format("{0:dd MMM yyyy}", now)); // Imprime: "24 oct. 2016"
Console.WriteLine(String.Format("{0:dd 'de' MMMM 'de' yy}", now)); // Imprime: "24 de octubre de 16"
```  

En la última línea, ¿viste cómo es que "de" está escrito entre comillas simples? a esto se le llama *"escapar"* una cadena, ya que de otro modo (de haber puesto "de" sin comillas simples) el resultado de `Format` habría sido: "24 24e octubre 24e 16". Siempre que deseemos que un caracter sea ignorado para cuando se realice el formato, tenemos que *escaparlo*.

Puedes consultar todos los modificadores de formato para fechas en <a href="https://msdn.microsoft.com/en-us/library/8kb3ddd4(v=vs.110).aspx" target="_blank">el sitio de MSDN</a>.  

## Relleno o padding  
La posibilidad de agregar relleno a las cadenas formateadas es una de las características que yo acabo de descubrir. Normalmente concatenaba una cadena vacía con la que quería mostrar y obtenía una subcadena para obtener el resultado deseado, sin embargo, podemos irnos olvidando de eso, ya que `Format` también nos lo ofrece. Cambia un poco la sintaxis, pero no es nada del otro mundo: ahora es necesario agregar otro número (separado por una coma), inmediatamente después del índice del parámetro y antes de los `:` en caso de que los contenga. 

El número que agreguemos representará la cantidad de relleno que se deberá agregar, lo mejor es que nos permite agregar *padding* tanto a la izquierda como a la derecha, dependiendo de si el número que agregamos es positivo o negativo:  
 
```csharp  
Console.WriteLine(String.Format("I|{0,10}|{0,-10}|D", 1300));    // Imprime: "I|      1300|1300      |D"
Console.WriteLine(String.Format("I|{0,10}|{0,-10}|D", -52));     // Imprime: "I|       -52|-52       |D"
Console.WriteLine(String.Format("I|{0,10}|{0,-10}|D", 0));       // Imprime: "I|         0|0         |D"
```  

Para terminar, podemos combinar todos los tipos de formato con *padding* y modificadores para presentar datos de una manera más ordenada, por ejemplo, en una tabla:
  
```csharp  
var data = new[]
{
    new { Dato1 = 0, Dato2="México", Dato3 = DateTime.Now },
    new { Dato1 = 2, Dato2 = "Canadá", Dato3 = DateTime.Now.AddDays(3) },
    new { Dato1 = 10, Dato2 = "Panamá", Dato3 = DateTime.Now.AddDays(-2) },
    new { Dato1 = 0, Dato2 = "Perú", Dato3 = DateTime.Now.AddMonths(-2) }
};
foreach (var item in data)
{
    Console.WriteLine("|{0,10:000}|{1,10}|{2,10:dd-MM}|", item.Dato1, item.Dato2, item.Dato3);
}
```  

Con lo cual obtendremos algo así:  

<pre>
|       000|    México|     24-10|  
|       002|    Canadá|     27-10|  
|       010|    Panamá|     22-10|  
|       000|      Perú|     24-08|  
</pre>

Cuando un formato no se pueda aplicar, generalmente cuando el tipo de dato no es compatible con el formato elegido, se obtendrá una excepción del tipo `FormatException`. Generalmente esto no debería ocurrir puesto que tu controlas el código, pero sin duda debes tener cuidado de manejar las excepciones si crees que se podrían presentar.  

## Console.WriteLine  
Por si te diste cuenta, hay algunos casos en que uso únicamente el método `WriteLine` sin llamar a `Format`, y es que el primero tiene una sobrecarga que ejecuta el segundo, pero imprime directamente el resultado sin necesidad de que tengas que llamarlo tu explícitamente. Muy útil, la verdad.  
