---
layout: post
title: "Casting: implicit y explicit en C#"
date: 2017-05-30 19:00:01
author: Antonio Feregrino
summary: Crea conversiones personalizadas entre tipos de dato, una técnica parecida a la sobrecarga de operadores... que debe usarse con moderación.
featured_image: casting.jpg
images_folder: /aprende-c-sharp/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Casteo
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Parece que fue ayer cuando hablamos de <a href="..\sobrecarga-operadores">la sobrecarga de operadores</a>, y hoy toca hablar de algo ligeramente parecido.

Son las palabras `implicit` y `explicit`, estas palabras sirven para declarar conversiones entre tipos de dato para los tipos de dato que creemos nosotros.

Para crear una conversión se usa la sintaxis:  

```csharp  
public static [implicit|explicit] operator <tipo destino>(<tipo origen> identificador)
{
    return <instancia de tipo destino>    
}
```  

 - La especificación de una conversión siempre es `static`.
 - Se usa `implicit` cuando queremos que la conversión suceda "silenciosamente", mientras que usamos `explicit` cuando queremos que el programador se vea forzado a especificar la conversión
 - El tipo destino es el tipo de dato al que queremos llegar
 - El tipo origen es el tipo que queremos convertir  

Pero qué mejor que verlo con un ejemplo: Imagina que tienes una clase llamada `Persona`:  

```csharp  
public class Persona
{
    public string Nombre { get; set; }
```  

### Conversión explícita  

Podrías especificar una conversión explícita de un tipo de dato `string` a `Persona`: 

```csharp  
    public static explicit operator Persona(string nombre)
    {
        return new Persona {Nombre = nombre};    
    }
```  

De este modo, se podrían crear objetos del tipo persona de esta manera:  

```csharp  
Persona p2 = (Persona)"Seto Kaiba";
var p3 = (Persona)"Joey Wheeler";
//Persona p4 = "Téa Gardner";
```  

Tanto `p2` y `p3` son objetos del tipo persona, por otro lado `p4` no es una sintaxis válida, ya que en la especificación de la conversión, nosotros establecimos que la conversión debe ser explícita, es decir, indicada por el programador.  

### Conversión implícita  

Por otro lado, podrías especificar una conversión implícita para convertir de `Persona` a `string`:

```csharp  
    public static implicit operator string(Persona p)
    {
        return p.Nombre;
    }
```  

De este modo, el siguiente código es válido:

```csharp  
string nombreP1 = p1;
string nombreP2 = p2;
var nombreP3 = (string)p3;
```  

En este caso, no es necesario especificar la conversión (<a href="..\var-en-c-sharp">a menos que estemos usando <code>var</code></a>), puesto que esta se realiza automáticamente.

## Conversión entre tipos personalizados  

Entre los ejemplos de conversiones, los más comunes son los que convierten entre unidades de medida. Considera un tipo de dato `Yarda` y otro `Metro`, ambos con conversiones explícitas especificadas de la siguiente manera:  

```csharp  
// Metro
public static explicit operator Metro(Yarda m)
{
    var metro = new Metro(m.Valor / 0.9144m);
    return metro;
}

// Yarda
public static explicit operator Yarda(Metro m)
{
    var yarda = new Yarda(m.Valor * 0.9144m);
    return yarda;
}
```  

Además de otro par de conversiones para transformar de decimal a `Metro` o `Yarda` (recuerda siempre que el código está <a href="https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Casteo" target="_blank">disponible para ser descargado</a> y puedas probarlo y modificarlo tu mismo).

Entonces podemos hacer algo como esto después de especificar todas las conversiones: 

<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
var metro1 = new Metro(5);
var yarda1 = new Yarda(5);
Console.WriteLine(metro1);
Console.WriteLine(yarda1);
```  
	</div>
    <div class="pure-u-1-2"> 
<pre>
Resultado:
5.000 metro(s)
5.000 yarda(s)

</pre>  
</div>
</div>

<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
Metro metro2 = 10;
Yarda yarda2 = 10;
Console.WriteLine(metro2);
Console.WriteLine(yarda2);
```  
	</div>
    <div class="pure-u-1-2">
<pre>
Resultado:
10.000 metro(s)
10.000 yarda(s)

</pre> 
</div>
</div> 

<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
Yarda yarda3 = (Yarda)metro2;
Metro metro3 = (Metro)yarda2;
Console.WriteLine(metro3);
Console.WriteLine(yarda3);
```  
	</div>
    <div class="pure-u-1-2"> 
<pre>
Resultado:
10.936 metro(s)
9.144 yarda(s)

</pre>  
</div>
</div>

<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
Metro metro4 = (Metro)yarda3;
Yarda yarda4 = (Yarda)metro3;
Console.WriteLine(metro4);
Console.WriteLine(yarda4);
```  
	</div>
    <div class="pure-u-1-2">
<pre>
Resultado:
10.000 metro(s)
10.000 yarda(s)

</pre>
</div>
</div>

## Pero...  

Al igual que la sobrecarga de operadores, la especificación de conversiones es algo que debe hacerse con moderación y siempre que tenga sentido dentro del contexto de nuestra app, no solo es hacerlo porque se ve bien o es un truco que C# nos ofrece.