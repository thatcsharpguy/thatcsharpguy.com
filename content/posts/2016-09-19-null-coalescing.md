---
layout: post
title: Operador null coalescing en C#
date: 2016-09-19 19:00:01
author: Antonio Feregrino
excerpt: Este operador te ayudará a escribir con mayor claridad el código, al tiempo que te ahorras escribir algunos cuantos ifs en tu código.
featured_image: null-c.png
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/nullcoalescing
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Pese a estar desarrollando en C# desde hace tiempo, tengo que aceptar que hay algunas cosas que me tardé en conocer... entre ellas, el operador `??` . Seguramente ya lo conoces, pero si no, te invito a seguir leyendo para que lo hagas. En realidad, aunque lo conozcas te invito a seguir leyendo, espero apreandas algo nuevo.

Este operador es básicamente una especie de <a href="../../tv/azucar-sintactica" target="_blank">azúcar sintáctica</a> para escribir una **asignación condicional** de tipos por referencia. En otras palabras (en código), observa las siguientes líneas:

```csharp  
string personName;
if (inputName == null)
    personName = "no name";
else
    personName = inputName;
Console.WriteLine(personName);
```  

Si `inputName` es `null`, el valor de `personName` será *"no name"* para cuando el método `WriteLine`, todo eso en 4 líneas. Pero esta no es la única forma de realizar la asignación dependiendo del valor, también podríamos haber hecho uso del <a href="https://msdn.microsoft.com/en-us/library/be21c7hw(VS.94).aspx" target="_blank">operador ternario</a>:  

```csharp  
personName = inputName == null ? "no name" : inputName;
Console.WriteLine(personName);
```  

### Null coalescing

Que producirá el mismo resultado si `inputName == null`, todo en 1 sola línea... sin embargo esta sigue sin ser la manera más corta de realizar una asignación de este tipo, la más corta se consigue a través del operador `??`, llamado **null coalescing** (o de uso combinado de NULL, en español):

```csharp  
personName = inputName ?? "no name";
Console.WriteLine(personName);
```  

Y listo, realiza la misma acción que los dos bloques de código anteriores, pero en menor cantidad de caracteres.

Si aún no has inferido su funcionamiento, es el siguiente: la asignación comienza del lado derecho del signo `=`, y va de izquierda a derecha evaluando los las expresiones en busca del primer valor no nulo, en cuanto alguno sea distinto de `null`, lo asignará a la variable ubicada a la izquierda de `=`.  

### Encadenamiento

Otra de sus particularidades es que permite ser *encadenado*: si colocamos un operador tras otro, se aplicará el mismo principio de buscar desde la izquierda el primer valor no nulo para ser asignado:  

```csharp  
personName = inputName ?? GetRandomName() ?? TryGetName() ?? "no name";
```  

Si esta misma asignación se intentara haecer con sentencias `if` o con operadores ternarios, la cantidad de código a escribir aumentaría considerablemente.

### Tipos nullables   

Uno de sus usos es junto a los <a href="../tipos-nullables-en-c-sharp">tipos nullables</a>: 

```csharp  
int? teamAPoints = null;
int? teamBPoints = 5;

var difference = (teamAPoints ?? 0) - (teamBPoints ?? 0);
Console.WriteLine("Difference " + difference);
```  

### Carga diferida
También suele ser muy frecuente que este operador sea usado para implementar la *carga diferida* o <a href="https://es.wikipedia.org/wiki/Lazy_loading" target="_blank">lazy loading</a> o el patrón *singleton*:

```csharp  
private static Random _random;
public static Random Random
{
    get
    {
        return _random ?? (_random = new Random(DateTime.Now.Second));
    }
}
```  

Para la primera vez que llamemos a `Random`, se ejecutará la parte derecha de la instrucción y para llamadas subsecuentes, únicamente nos devolverá eñ valor de `_random`.

### Observaciones

Es importante señalar que para que el operador funcione, los tipos a evaluar deben ser del mismo tipo (o al menos tener conversión implícita) ya que de otro modo, el compilador nos mostrará un error e impedirá la ejecución del programa. El siguiente segmento de código es inválido, ya que `personName` es una cadena y `teamAPoints` es *nullable de int*

```csharp  
var invalid = personName ?? teamAPoints;
```  

### En C# 6  
Las cosas buenas no acaban ahí, este operador cobra mayor relevancia cuando lo combinamos con una de las nuevas funciones de C#, <a href="../c-sharp-seis/#null-conditional-operator" target="_blank"><em>null-conditional operator</em></a> (`?.`) podemos la funcionalidad que deseamos y sin usar tantas líneas de código:

```csharp  
var randomOrNot = MainClass.Random?.Next(0, 10) ?? 0;
```  

### Ventajas  
Para ser honesto, aunque todo depende de quién lo mira, al operador `??` le encuentro únicamente ventajas:  

 - Es más corto y fácil de escribir  
 - Es más fácil de leer
 - Podemos implementar *singletons* o *carga diferida*
 - Puede ser encadenado uno tras de otro

¿Tu le encuentras otra ventaja o una desventaja? mándame un tuit, un correo o deja un comentario en la parte de abajo.
