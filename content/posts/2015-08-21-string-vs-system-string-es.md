---
layout: post
title: Un post sobre alias, ¿string vs. System.String?
date: 2015-08-21 13:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: He de aceptarlo, una de las cosas que más dudas me causaba cuando inicié a programar usando C# era sobre cuál era la diferencia entre usar string o System.String, la idea que yo tenía al respecto que estaba totalmente equivocada.
lang: es
featured_image: featured.jpg
alias: /string-vs-system-string-es/index.html
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

He de aceptarlo, una de las cosas que más dudas me causaba cuando inicié a programar usando C# era sobre cuál era la diferencia entre usar string o System.String, la idea que yo tenía al respecto que estaba totalmente equivocada. Tenía la mala creencia de que `string` y `String` (sí, la diferencia es la mayúscula inicial) eran cosas un tanto distintas cuando resulta que no es así.  

### Alias  
Resulta que `string` y `System.String` (o simplemente `String`) son lo mismo, es decir, en nuestro código podemos fácilmente intercambiar ambas palabras sin problema alguno. Por poner otro ejemplo, usando el alias para `Int32`, podemos escribir de cualquiera de las dos siguientes maneras y el código significará lo mismo.  
<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
int abc = 123;
Int32 xyz = 123;
```  
	</div>
    <div class="pure-u-1-2">
```csharp  
Int32 abc = 123;
int xyz = 123;
```  
	</div>
</div>  

#### ¿Referencias o valores?  
Como parte de mis falsas creencias, pensaba que al usar `System.Int32` estaba creando tipos de dato por referencias (o *envoltorios*, como en Java) con todo lo que eso representa, pero repito: No es así. Todos los tipos de valor permanecen así independientemente de cómo los hayamos declarado. Eso sí, no olvides que `string` y por tanto `String` son un tipo de referencia. Abajo pongo una tabla con algunos alias de C#:
<table>
	<thead>
		<tr>
			<th>Alias</th>
			<th>Framework</th>
			<th>Tipo</th>
		</tr>
	</thead>
	<tr>
		<td>int</td>
		<td>System.Int32</td>
		<td>Valor</td>
	</tr>
	<tr>
		<td>bool</td>
		<td>System.Boolean</td>
		<td>Valor</td>
	</tr>
	<tr>
		<td>float</td>
		<td>System.Single</td>
		<td>Referencia</td>
	</tr>
	<tr>
		<td>object</td>
		<td>System.Object</td>
		<td>Referencia</td>
	</tr>
</table>	 


#### ¿Para qué existen?  
Puede que te estés preguntando eso, y la verdad es que no sé. Muchos sugieren que es por cuestión de hábito, ya que los alias se parecen (si no es que son iguales) a las declaraciones de variables para lenguajes de programación más antiguos que C#, como C o C++. Para un programador sería más sencillo migrar de un lenguaje a otro si encuentra cosas parecidas a las que está acostumbrado, aunque sean detalles pequeños como este.

#### ¿Cuál es correcto?  
Al significar lo mismo en términos de programación su uso es cuestión de gustos, pero yo prefiero usar el alias para declarar variables, tipos de dato de retorno y parámetros de método. Mientras que prefiero usar el nombre "completo" para hacer referencia a métodos y constantes de cada tipo, por ejemplo  
<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
// Yo prefiero...
int abc = Int32.Parse("123");
string emptyString = String.Empty;
```  
	</div>
    <div class="pure-u-1-2">
```csharp  
// ... a esto
Int32 abc = int.Parse("123");
String xyz = string.Empty;
```  
	</div>
</div>  

#### Lo que sigue  
En el siguiente post les platicaré de una forma en la que podemos crear nuestros propios alias y así también tomar ventaja de las características que nos ofrece el lenguaje.  
