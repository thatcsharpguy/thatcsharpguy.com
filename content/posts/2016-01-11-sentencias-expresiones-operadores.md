---
layout: post
title: Sentencias, expresiones y operadores
date: 2016-01-11 19:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Hace tiempo yo pensaba que sabía programar más o menos bien en C#, pero había ocasiones en las que leyendo documentación me encontraba con ciertas palabras que no lograba comprender, entre ellas estaba las palabras "sentencia" y "expresión".
featured_image: featured.png
github: https://github.com/ThatCSharpGuy/CsSeis
lang: es
tweet_id: 686733690899181568
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Hace tiempo yo pensaba que sabía programar más o menos bien en C#, pero había ocasiones en las que leyendo documentación me encontraba con ciertas palabras que no lograba comprender, entre ellas estaba las palabras "sentencia" y "expresión", este es otro de esos posts que me hubiera gustado leer antes.

## Sentencias (Statements) 

Todas las “líneas de código” con acciones que nuestro programa realiza son sentencias, desde la declaración de una variable hasta el control de flujo del programa con un `if` o un `case`. Las sentencias de este tipo se conocen como *sentencias de una línea*.  
  
Algunos ejemplos de sentencias de una sola línea son:  

<table class="pure-table pure-table-bordered">
    <thead>
	        <tr>
            <th>Tipo</th>
            <th>Descripción</th>
        </tr>
    </thead>

    <tbody>
        <tr>
            <td>Declaraciones</td>
            <td>Crea una nueva variable o constante en el programa como, por ejemplo <code>string t;</code> o <code>const ix = 0;</code> usualmente se encadena con una sentencia de asignación.</td>
        </tr>
        <tr>
            <td>Expresiones</td>
            <td>Sentencias que calculan un valor.</td>
        </tr>
        <tr>
            <td>Salto</td>
            <td>Son otro elemento para controlar el flujo del programa, sirven para mover el punto de ejecución del programa, por ejemplo <code>goto</code>, <code>break</code> y <code>return</code></td>
        </tr>
        <tr>
            <td>Vacío</td>
            <td>Sí, existe una sentencia vacía y es <code>;</code>.</td>
        </tr>
    </tbody>
</table>
  
También existen sentencias más complejas, que no son de *una sola línea* en las cuales se suelen agrupar más sentencias usando `{` y `}` como delimitadores, a esta acción se le conoce como *anidamiento*, 

## Expresiones  
Las expresiones son un tipo especial de sentencias que se componen de uno o más operandos en conjunto con cero o más operadores, la diferencia con una sentencia común es que una expresión siempre puede ser evaluada a un solo valor. En la mayoría de los casos **una expresión es una sentencia que siempre devuelve un valor**. Ya sea que la expresión devuelva una referencia a un objeto o un valor.  
  
Algunos ejemplos de expresión son los siguientes:  

### Operador new  
```csharp  
new Object(); 
```  
Cuando usamos el operador `new` para instanciar un objeto, el constructor de la clase (en este caso `Object`) es el operando y el resultado de esta expresión es una referencia a una instancia de la clase (en este caso, de `Object`).

### Incremento y decremento
```csharp  
i++;
--i;
```  
Los operadores `++` y `--` son denominados de incremento y decremento, el resultado de la expresión varía en función de la posición en la que estén colocados respecto al operando (que en nuestro caso es `i`).  

 - Antes del operando: es el valor del operando antes de ser incrementado/decrementado
 - Después del operando: es el valor del operando después de ser incrementado.

### Llamada (call)
```csharp  
ConvertInt(i);

i.ToString();
```  
Las llamadas a métodos también son ejemplos de expresiones, estas expresiones no requieren de un operador como las dos anteriores, basta con escribir el nombre del método y agregarle sus argumentos en caso de ser necesarios.  
  
Suponiendo que tenemos la siguiente firma de método `decimal ConvertInt(int i)`, la primera expresión en el código anterior tendrá como resultado un `decimal`, mientras que la segunda tendrá como resultado una cadena.
  
<br />  
  
En el ejemplo anterior de código, las expresiones estaban escritas solas y funcionan como están. Sin embargo, en C#, no todas las expresiones pueden existir de esa manera, muchas tienen que existir en acompañadas por una sentencia para que el programa compile, de otra manera obtendríamos el error de compilación `CS0201: Sólo se pueden utilizar las expresiones de objeto assignment, call, increment, decrement y new como instrucción`. Como ejemplo de dichas expresiones tenemos:  

### Literales  
```csharp  
int n = 10;
var hola = "Hola";
```  

En este caso estamos frente a **las literales, las expresiones más simples que se pueden usar en C#**, la primera expresión es `10`, que se evalúa a un tipo por valor tipo `int`; la segunda expresión es `"Hola"` que se evalúa a un tipo por referencia tipo string. Si se hubiera usado únicamente `10;` u `"Hola";` sin sentencia, un error de compilación habría aparecido.  

### Literales y operadores  
```csharp  
int i = 3 + 2;
```  
    
En este caso `3 + 2` es una expresión en la que el operador es `+` cuyo resultado es un tipo por valor del tipo `int`, la sentencia que lo acompaña es declaración `int i`. De haber usado solo `3 + 2;` habríamos obtenido un error de compilación.

### Nombres simples    
```csharp  
string hola2 = hola;

return hola; // Recordando de 'Hola' es un cadena
```  

Dentro del código anterior, `hola` es un nombre simple, es el nombre de una variable de tipo string. Pero además de eso, es una expresión que se evalúa al valor que contenga en ese momento. En el primer caso, se usa una sentencia de asignación y en el segundo está acompañada de la sentencia `return`, ya que de otro modo si hubiéramos usado `hola;` de nuevo tendríamos un error de compilación.

## Operadores
Los operadores son elementos dentro de C# que cuando se aplican a los operadores para devolver un valor a partir de ellos. Dentro del lenguaje existen tres tipos de operadores:
  
### Unarios  
Que son los que toman un solo operando, entre ellos están `++`, `new`, `typeof`, `~` y `(T)`.  

### Binarios 
Son los que toman dos operandos, entre ellos están `*`, `+`, `>`, `==`.

### Ternarios  
Son los que toman más de dos operandos, el único caso dentro del lenguaje es el *operador condicional* `?:` que toma tres operandos.  
  
Pueden echarle un ojo a la lista completa de operadores en <a href="https://msdn.microsoft.com/en-us/library/ms173145.aspx" target="_blank" rel="nofollow">este enlace</a>.  
  
Es importante destacar que algunos de los operadores pueden ser sobrecargados para modificarlos de acuerdo a nuestras necesidades, pero ese es tema de otro post. Recuerda que si tienes dudas o algo no quedó tan claro no dudes en mandarme un tuit o un correo electrónico.
