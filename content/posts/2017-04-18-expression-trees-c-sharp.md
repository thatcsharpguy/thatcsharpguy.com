layout: post
title: Árboles de expresiones en C#
date: 2017-04-18 19:00:01
author: Antonio Feregrino
excerpt: Un árbol de expresiones permite examinar el código de un delegado en tiempo de ejecución, permitiéndote así aprovechar al máximo los recursos de tu sistema.
featured_image: featured.jpg
images_folder: /aprende-c-sharp/expressions/
github: https://github.com/ThatCSharpGuy/ArbolesDeExpresion
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp

El tipo de dato `Expression` no es un tipo de dato convencional, no es algo que le mostrarás al usuario final de tu app en la pantalla. Más bien es un tipo de dato diseñado para ser usado por otros desarrolladores. Este tipo de dato fue introducido junto con LINQ en .NET 3.5. Seguramente tu mismo lo has usado sin darte cuenta, el lugar más común para encontrarlo es como parámetro del método de extensión `Where` de cualquier colección que implementa la interfaz `IQueryable`:   

<img src="/images/aprende-c-sharp__expressions__inwhere.jpg" title=""Where clause"" />

"¡Pero si eso es una expresión lambda!" podrías decirme... y tendrías toda la razón, sin embargo, existe una conversión implícita entre `Expression<TDelegate>` y una expresión lambda, que es lo que generalmente vemos nosotros como desarrolladores.  

### ¿Y luego? 

Las expresiones permiten inspeccionar el código que forma determinada expresión lambda. Por ejemplo, de la imagen anterior podríamos saber que la expresión lambda recibe un parámetro llamado `a` que es del tipo entero, que se realiza una operación módulo y que se compara contra cero el resultado. Todo esto sin necesidad de decompilar el código ni nada por el estilo. 

#### Trabajando con la expresión

Los tipos `Expression` fueron creados con la intención de ser usados <a href="..\func-y-action-en-c-sharp">con delegados</a>, así que para comenzar a usarlos es necesario especificar qué tipo de delegado está esperando la expresión. Por ahora nosotros vamos a crear un método que inspeccione una expresión lambda que recibe un entero como argumento y devuelve un valor booleano:  

```csharp  
void Inspecciona(Expression<Func<int, bool>> expression)
{
    Console.WriteLine("== Examinando \"" + expression + "\" ==");
    Console.WriteLine("Expresión: " + expression.NodeType);
    var binaryExpression = expression.Body as BinaryExpression;
    if(binaryExpression != null)
    {
        Console.WriteLine("La expresión es " + binaryExpression.NodeType); 					
        Console.WriteLine("Sus componentes son:");
        Console.WriteLine("\tLeft: " + binaryExpression.Left + 
                          " (" +binaryExpression.Left.NodeType  +")");
        Console.WriteLine("\tRight: " + binaryExpression.Right +
                          " (" + binaryExpression.Right.NodeType + ")");
        return;
    }
    var constantExpression = expression.Body as ConstantExpression;
    if(constantExpression != null)
    {
        Console.WriteLine("El cuerpo de la expresión es constante");
        Console.WriteLine("\tValor: " + constantExpression.Value);
        return;
    }
}
```  

Lo sé, en el código de arriba ocurren muchas cosas, vamos a ver las más importantes:  

 - El `Expression<Func<int, bool>> expression` en el argumento del método indica que el método recibe una expresión que envuelva a una expresión lambda con la firma `Func<int, bool>`
 - Con la propiedad `NodeType` podemos conocer con qué tipo de expresión estamos trabajando. Esta propiedad está disponible para cualquier tipo de `Expression`  
 - La propiedad `Body` nos permite acceder al cuerpo de una expresión lambda. Y a su vez, `Body` es también una expresión, es por eso que primero intento convertirla en una `BinaryExpression` y luego en una `ConstantExpression`. Existe una <a href="https://msdn.microsoft.com/en-us/library/system.linq.expressions.expression(v=vs.110).aspx#Anchor_0" target="_blank">gran lista</a> de tipos de expresiones con los que puedes trabajar.  
 - Ahora, después de saber si nuestra expresión es de determinado tipo, se puede acceder a las propiedades de ese tipo en específico. Por ejemplo, las expresiones binarias tienen dos propiedades `Left` y `Right` que almacenan referencias a las expresiones que la forman, mientras que una expresión constante tiene la propiedad `Value` que almacena su verdadero valor.  

### Árboles de expresiones  
En este punto es cuando surgen los árboles de expresiones, si te das cuenta, podemos ir formando un árbol a partir de una expresión lambda, para tratar de demostrar este punto, mira los siguientes ejemplos: 

```csharp  
Inspecciona((a) => true);
// Resultado:
//== Examinando "a => True" ==
//Expresión: Lambda
//El cuerpo de la expresión es constante
//	Valor: True
```  

<img src="/images/aprende-c-sharp__expressions__constant.png" title=""Constant expression"" />

```csharp  
Inspecciona((a) => a % 2 == 0);
// Resultado:
//== Examinando "a => ((a % 2) == 0)" ==
//Expresión: Lambda
//La expresión es Equal
//Sus componentes son:
//	Left: (a % 2) (Modulo)
//	Right: 0 (Constant)
```  

<img src="/images/aprende-c-sharp__expressions__binarysimple.png" title=""Simple binary expresion"" />

```csharp  
Inspecciona((a) => a % 5 == 0 && Math.Pow(a, 2) % 3 == 0);
// Resultado:
//== Examinando "a => (((a % 5) == 0) AndAlso ((Pow(Convert(a, Double), 2) % 3) == 0))" ==
//Expresión: Lambda
//La expresión es AndAlso
//Sus componentes son:
//	Left: ((a % 5) == 0) (Equal)
//	Right: ((Pow(Convert(a, Double), 2) % 3) == 0) (Equal)
```  

<img src="/images/aprende-c-sharp__expressions__binarycomplex.png" title=""Complex binary expresion"" />

## Uso en la vida real  
Sí, ya sé que tal vez no vayas por la vida escribiendo métodos que inspeccionen expresiones lambda. Pero tan solo es necesario que te pongas a pensar que gracias a los árboles de expresiones existen cosas como LINQ to SQL, Entity Framework, LinqToTwitter, entre otros.   

Lo que hacen estas librerías es tomar tu código C# en forma de expresiones, inspeccionarlo y "traducirlo" a SQL para hacer que tus consultas sean lo más eficientes posible aprovechando el poder de la base de datos. En el caso de LinqToTwitter sucede algo similar, solo que la "traducción" transforma tu código C# en una URL lista para ser consumida.  

### tldr

En cierto sentido podrías ver a una `Expression<TDelegate>` como un bloque de código no compilado del que podemos obtener información en tiempo de ejecución acerca de cómo está compuesto. Algo parecido a lo que se puede lograr con <a href="..\reflexion-c-sharp-es">la reflexión</a> pero con la certidumbre de que el código con el que estás trabajando, es válido.


### Cuidado  
Para finalizar el post quisiera hacer un servicio público a la comunidad que trabaja con Entity Framework o LINQ to SQL. Y es que, cuando trabajamos escribiendo código, a veces nos agrada facilitarnos la vida reusando código como en el siguiente ejemplo:  

```csharp  
// Filtro:
private bool ClienteAprobado(ClubUser user)
{
    return user.Approved && user.IsActive;
}

// Uso de filtro:
var usuariosAprobados = _context.Users.Where(ClienteAprobado);
```  

El enorme error del ejemplo anterior es que no está haciendo gran uso delos mecanismos que nos ofrece EF o L2S, sino que está cargando todos los registros a memoria y realizando el filtrado sobre los objetos, lo cual puede resultar muy costoso. Esto sucede porque el método que estamos empleando como filtro no se puede convertir a una `Expression` examinable, es solo un método.

La solución es simple y nos permitirá seguir reusando el código. Bastará con modificar el método que usamos como filtro para que devuelva una `Expression`:  

```csharp  
// Filtro:
private Expression<Func<ClubUser, bool>> ClienteAprobado()
{
    return (u) => u.Approved && u.IsActive;
}

// Uso de filtro:
var usuariosAprobados = _context.Users.Where(ClienteAprobado());
```  

Y listo, ahora si estaremos usando al máximo los beneficios de los frameworks :)