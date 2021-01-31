---
layout: post
title: Tipos de dato en C#
date: 2015-10-05 22:00:00
author: Antonio Feregrino
categories: aprende-csharp
summary: Para la mayoría de sus aplicaciones, C# es un lenguaje fuertemente tipado, en este post presento dos categorías de tipos que nos podemos encontrar cuando trabajamos con este lenguaje.
lang: es
featured_image: featured.jpg
alias: /tipos-dato-c-sharp/index.html
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Para fines de este post, diremos que C# es un lenguaje fuertemente tipado (aunque ya permite tipado dinámico y eso lo palticaré en otro post), lo que significa que cada variable, literal o constante debe tener un tipo declarado y dicho tipo no cambiará con el flujo del programa. Dentro de los tipos que ofrece C#, podemos encontrar dos grandes categorías:  

### Predefinidos  
Son los tipos que el lenguaje incorpora para almacenar valores comunes, entre ellos:  

- `bool` para valores binarios, como un *sí* o *no*, o `1` o `0`  
- `int` para valores enteros, que puede ir de `-2,147,483,648` a `2,147,483,647`  
- `char` para caracteres como `a`, `@` o `%`  
- `string` para una secuencia de caracteres  

La lista la completan: `byte`, `decimal`, `float`, `long`, `sbyte`, `short`, `uint`, `ulong`, `ushort` y `object`.  
  
A partir de estos tipos de dato se pueden crear otros tipos con la finalidad de satisfacer nuestras necesidades.  
  
### Compuestos    
Los tipos compuestos están formados a partir de los tipos predefinidos y nos ayudarán a modelar de manera más real los problemas con los que nos encontremos. Estos se crean usando las  palabras reservadas `interface`, `struct`, `enum` y `class`.  
  
Otra clasificación que podemos definir, es la siguiente, que clasifica los tipos de dato de acuerdo a como se gestionan en la memoria:  
  
### Referencia  
Se almacena en ellos una referencia a los datos, por ejemplo un tipo de dato `class` o `string`. 
  
### Valor  
Almacenan el valor del dato por completo, por ejemplo `struct` y `enum`.  
  
## Ejemplo
Para ilustrar esta última categoría, tomemos el siguiente ejemplo:  

<div class="pure-g">
<div class="pure-u-1-2">
```csharp  
struct Tiempo 
{
	public int Minutos;
	public int Horas;
}
// ...  
Tiempo t1 = new Tiempo();
Tiempo t2 = t1;
```  
</div>
<div class="pure-u-1-2">
```csharp  
class Tiempo 
{
	public int Minutos;
	public int Horas;
}
// ...  
Tiempo t1 = new Tiempo();
Tiempo t2 = t1;
```  
</div>  
</div>  
<div class="pure-g">
<div class="pure-u-1-2">
<img src="https://thatcsharpguy.github.io/postimages/tipos-dato-c-sharp/value-type.png" title="Tipos por valor" />
</div>
<div class="pure-u-1-2">
<img src="https://thatcsharpguy.github.io/postimages/tipos-dato-c-sharp/reference-type.png" title="Tipos por valor" />
</div>  
</div>  
<br />
Las imagenes debajo de los códigos tienen la intención de mostrar una simple representación de lo que pasa al ejecutar el código. Al hacer `t2 == t1` sobre la estructura de Tiempo, al ser un un *tipo por valor* se crea una copia, lo cual significa que cualquier modificación que se haga a `t1` no provocará ningún cambio sobre `t2`.  
Por otro lado, en el caso de la clase Tiempo, si se hiciera alguna modificación sobre `t2`, `t1` también se vería afectado al ser un *tipo de referencia*.  
  
<br />
En futuros post seguré explorando algunos detalles escenciales de C#, como arreglos, métodos y clases.  
