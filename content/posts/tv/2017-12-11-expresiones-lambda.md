---
title:  Las expresiones lambda
date:  2017-12-18 18:00:00
youtube:  DvE-J-jgB_A
images_folder:  /tv/lambdas/
summary:  Las expresiones lambda son bloques de código que NO están asociados con un identificador.
featured_image:  featured.png
tweet_id:  942879414903300096
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

Las expresiones lambda o funciones lambda, o, por su nombre correcto: las funciones anónimas son bloques de código que NO están asociados con un identificador... es decir, en tiempo de ejecución no tienen un nombre asociado. Como por ejemplo estas que son funciones (o métodos) pero si tienen un nombre:

_ejemplos_

Como puedes ver, todos estos ejemplos las funciones están asociados fuertemente con un nombre. Mientras que en el caso de las expresiones lambda esto no es necesario. Estos son ejemplos de expresiones lambda que definen lo mismo que los métodos anteriores.

_ejemplos_

## Sintaxis

Para definirlas la sintaxis varía de lenguaje a lenguaje, sin embargo generalmente la sintaxis es algo como esto:

```
Argumentos - Operador - (Contenido)Expresión
```

### C#
En C# se define de la siguiente manera:

```
(int x) => { return x*x; };
```

```
(int x) => 
{ 
	var x_squared = x*x;
	return x_squared; 
};
```

En C# podemos no usar los paréntesis siempre y cuando se trate de una expresión lambda de 1 solo argumento... ah, y podemos quitar los corchetes, y la palabra `return` siempre y cuando el contenido de la lambda sea una sola expresión.  

```
x => x*x;
```

### Python  

Usando Python la cosa cambia un poco, pero verás que la estructura sigue siendo la misma, tenemos algo así:

```
lambda x: x*x;
```

En Python no hay nada más que hacer, no hay que usar paréntesis para especificar multiples argumentos, basta con separarlos con una coma. Sin embargo existe una desventaja: las lambdas en este lenguaje solamente deben contener una expresión. 

### JavaScript 

JavaScript es muy similar a C#:

```
exp1 = (x) => { return x*x; };
```

```
exp4 = x => 
{ 
	x_squared = x*x;
	return x_squared; 
};
```

En el sentido de que se puede reducir la expresión lambda siempre y cuando el cuerpo de la expresión lambda sea... una sola expresión.

```
exp3 = x => x*x;
```

Existen también en Java (aunque ahí son un poco raras), en Go, Haskell, C++, PHP... y en muchos más. 

## Razón de ser

Pero, ¿para qué querríamos escribir este tipo de expresiones?  Las expresiones lambda nos ayudan a

 - Evitarnos el escribir código innecesario, y a la vez evitarnos llenar nuestro código de lógica que solamente vamos a usar una sola vez o en una sola parte del código. Si, por ejemplo, necesitas ejecutar una operación sobre un conjunto de elementos, tal vez sea mejor usar una expresión lambda que crear un método o una función que nadie más va a usar.

 - Reducir la complejidad de nuestro código al evitarnos tener que navegar entre lineas para llegar a la definición de una función. La lectura del código se hace más sencilla, puesto que la definición del método está más a la vista de quien la está usando y no tiene que andar navegando entre todo el laberinto de código.  

 - Nos ayudan a crear funcionalidad que luego puede ser pasada como argumentos a otras funciones, llamadas funciones de orden superior. Esto no es una cosa exclusiva de este tipo de expresiones, pero sin duda lo hace más sencillo.
  
Como podrás imaginarte, las funciones anónimas están relacionadas muy fuertemente con la programación funcional, de la cual ya les hablé en el pasado, sin embargo no es el único paradigma de programación en el que se le puede encontrar.