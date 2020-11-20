---
layout: post
title: ¿Qué es var en C#?
date: 2015-10-29 20:00:00
author: Antonio Feregrino
categories: c-sharp
summary: Usar la palabra var significa que estamos dejándole al compilador la tarea de asignar un tipo a nuestras variables.
featured_image: featured.jpg
lang: es
ideone: http://ideone.com/fork/iu3Pni
tweet_id: 659915600320471040
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Como programadores hay muchas ventajas que nos otorga C#, ya sea a la hora de escribir nuestros programas o a la hora de ponerlos a funcionar. En este post les voy a hablar acerca de una de las ventajas al escribir nuestros programas, pero para eso, veamos antes este código:  
```csharp  
var numeroElementos = 5;
var otroNumero = 5L;
var nombre = "Paquito";
var x = nombre + numeroElementos;
var y = numeroElementos + otroNumero;
var sb =  new StringBuilder();
var buenEstudiante =  new Persona("Estudiante");
```  
**¿Uhhhh?** ¿entonces qué significa `var`?  

### var  
Pues bien, usar la palabra <strike>reservada</strike> `var` significa que estamos dejándole al compilador la tarea de asignar un tipo a nuestras variables. Llamamos a esto *tipado implícito*, así nos quitamos de encima la tarea de escribir los tipos completos. Entonces:  

- `var numeroElementos = 5;` &#8594; `int numeroElementos = 5;`  
- `var otroNumero = 5l;` &#8594; `long otroNumero = 5l;`
- `var nombre = "Paquito";` &#8594; `string nombre = "Paquito";`    
<br />  

Pero no solo eso, también podemos usar `var` para instanciaciones un poco más complejas:  

- `nombre + numeroElementos` es `string + int` que resulta en `string`, por lo tanto tambièn pudimos haber escrito: `string x = nombre + numeroElementos;`  
- `numeroElementos + otroNumero` es `int + long` que resulta en `long`, por lo tanto también pudimos haber escrito `long y = numeroElementos + otroNumero`    
<br />

Tambén podríamos pensar que `var` funciona solo para tipos primitivos del lenguaje, sin embargo no es así, como lo demuestra:  

- `var buenEstudiante = new Persona("Estudiante")` en la que podemos reemplazar `var` por `Persona`, o
- `var sb = new StringBuilder();` en la que puede ser reemplazada por `StringBuilder`  
  
### Casos especiales  
Si bien es posible usar `var` para cualquier instanciación de una variable como en los casos anteriores, no es común usarla ya que a lo mucho nos está ahorrando algunas letras por cada variable. Sin embargo hay casos en los que su uso se vuelve bastante relevante y hasta necesario.  
<br />
Un caso en el que es bastánte práctico usarlo es cuando estamos trabajando con LINQ (LINQ es un tema que merece su propio post, y lo tendrá), ya que muchas veces no es tan claro el resultado de una sentencia LINQ, por lo tanto es común encontrar declaraciones como:
```csharp  
var listaMenores = personas.Where(p => p.Edad < 10).ToList();

var listaMayores = from p in personas
				   where p.Edad > 10
				   select p;
```  

Un caso en el que es **completamente necesario** el uso de `var` es cuando dentro de nuestro código usamos tipos anónimos, en pocas palabras un tipo anónimo es un objeto creado "al vuelo" sin declarar una clase. Aunque estos, al igual que LINQ, merecen su propio post (que también haré).  

### Limitaciones
El *tipado implícito* tiene algunas limitantes, una de ellas parecería ser bastante obvia, y esta es que no se puede escribir una declaración como:
```csharp  
var indeciso = null;
```  
Ya que el compilador no puede inferir el tipo de `null`, si lo intentamos obtendremos un bonito error de compilación.
La segunda limitante es que únicamente podemos usar `var` para variables locales, es decir, únicamente dentro de la implementación de nuestros métodos. En otras palabras, no podemos hacer algo como:
```csharp  
class Persona 
{
	var Edad = 10;
	
	public Persona( // ...
```  
    
### Desventajas
La única gran desventaja que veo en esta caracterísitca es que si se abusa de ellos y en combinación con tipos de dato complejos podemos escribir código potencialmente inentendible, al grado que ni nosotros mismos podamos entender lo que escribimos.  
Personalmente siempre uso `var` para declaraciones de tipos de dato simples como enteros o cadenas. 

### Importante  
Es importante repetir esto: **C# sigue siendo fuertemente tipado, con var lo único que hacemos es dejarle la tarea al compilador de asignar un tipo para nuestra variable al momento de la compilación**.  
<br />
En el post original había mencionado que `var` es una palabra reservada, cuando en realdad, no lo es. Depende del contexto.

### Lo que sigue  
En este post hablamos de dos nuevos conceptos: LINQ y tipos anónimos, y sobre uno de ellos dos será el post siguiente, ¿tienes preferencia sobre alguno? házmelo saber vía <a href="https://twitter.com/intent/tweet?text=@io_exception">Twitter</a> o por <a href="mailto:{{ MY_EMAIL }}?subject={{ title }}">correo electrónico</a>.

