---
layout: post
title: Operadores lógicos corto-circuitados
slug: cortocircuito-c-sharp
date: 2017-05-16 19:00:01
author: Antonio Feregrino
summary: Los operadores lógicos más usados tienen una peculiaridad que ayuda a mejorar su desempeño, pero a veces podría ser un comportamiento indeseado. Por suerte, C# cuenta con otros operadores que evitan este comportamiento..
featured_image: featured.png
images_folder: /aprende-c-sharp/cortocircuito/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/CortoCircuito
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

No hay duda de que conoces estos operadores: `&&` y `||`, sabes lo que hacen e inclusive los has usado más veces de las que lo recuerdas en tus aplicaciones. Pero, probablemente no sabes que cuando las usas, el lenguaje hace de las suyas tratando de mejorar el desempeño de tu aplicación. Considera el siguiente par de ejemplos:  

```csharp  
if(true || false) { } 
if(false && true) { } 
```  

¿Qué sentido tendría evaluar el lado derecho de ambos condicionales si ya sabemos que nada de lo que está ahí modificará el resultado? entonces en tiempo de ejecución sucede algo como lo siguiente:

<div class="pure-g">
    <div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/cortocircuito/or-sc.png" title="OR shortcircuitted" />
    </div>
    <div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/cortocircuito/and-sc.png" title="OR shortcircuitted" />
    </div>
</div>

El programa reconoce que nada de lo que ocurra después del operador cambiará el resultado, entonces evita evaluarlo, ahorrando así un poco de tiempo y recursos. Este comportamiento se debe a los operadores lógicos `&&` y `||`, que en realidad su nombre real es: operadores lógicos **corto-circuitados**  (**short-circuiting** logical operators).  

Esto sin duda es algo de lo que muy pocos tendrían queja, pero para aquellos casos en los que quisiéramos que los operadores lógicos evalúen todos los operandos, C# nos tiene una solución: los operadores lógicos **no corto-circuitados**: `&` y `|`, que en pocas palabras evalúan todos los operandos, sin importar que estos no vayan a modificar el resultado de la operación.

<div class="pure-g">
    <div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/cortocircuito/or.png" title="OR shortcircuitted" />
    </div>
    <div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/cortocircuito/and.png" title="OR shortcircuitted" />
    </div>
</div>

Para poner un ejemplo <a href=" https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/CortoCircuito" target="_blank">que puedes descargar</a>, considera el siguiente par de métodos que imprimen un mensaje y retornan un valor constante booleano:

```csharp  
static bool AlwaysTrue(string label)
{   
    Console.Write(label + ": " + true);
    return true;
}

static bool AlwaysFalse(string label)
{
    Console.Write(label + ": " + false);
    return false;
}
```  

Entonces, al ejecutar el siguiente código: 

```csharp  
Console.WriteLine("Ifs");
if (AlwaysFalse("If 1") && AlwaysTrue("If 1")) ;
Console.WriteLine();
if (AlwaysFalse("If 2") & AlwaysTrue("If 2")) ;
Console.WriteLine();
if (AlwaysTrue("If 3") || AlwaysTrue("If 3")) ;
Console.WriteLine();
if (AlwaysTrue("If 4") | AlwaysTrue("If 4")) ;
Console.WriteLine();
if (AlwaysTrue("If 5") | AlwaysTrue("If 5") && (AlwaysFalse("If 5") & AlwaysTrue("If 5")));
```  

Obtendremos como resultado:  

<pre>
Ifs
If 1: False

If 2: False
If 2: True

If 3: True

If 4: True
If 4: True

If 5: True
If 5: True
If 5: False
If 5: True
</pre>

## ¿Por qué?  

La razón de la existencia de ambos operadores radica en que los no corto-circuitados tienen un propósito más allá de operaciones de lógica condicional, son operadores de *bit a bit*, que están pensados para funcionar con valores enteros, en estas operaciones siempre se deben tomar en cuenta ambos operandos. El hecho de que funcionen también con valores booleanos solamente es un extra.  
