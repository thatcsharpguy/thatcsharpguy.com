layout: post
title: Creando nuestros propios alias
date: 2015-09-16 13:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Hace poco vimos que existen alias en C# para los tipos predefinidos de datos, y hasta ahí todo claro. Pero nosotros como programadores también podemos beneficiarnos de crear nuestros propios alias.
lang: es
featured_image: featured.jpg
github: https://gist.github.com/fferegrino/a81cd436e4d631f1cf4d
alias: /creando-propios-alias/index.html
tags: AprendeCSharp
featured_tag: AprendeCSharp

Hace poco vimos que existen <a href="/string-vs-system-string-es" target="_blank">alias en C# para los tipos predefinidos</a> de datos, y hasta ahí todo claro. Pero nosotros como programadores también podemos beneficiarnos de crear nuestros propios alias. Hay dos maneras de hacer uso de los alias:  

### Para tipos de dato  
¿No sería fabuloso poder "cambiarle" (entre comillas porque en realidad solamente le estamos dando otro nombre) el nombre a <code>int</code>? tal vez sea mejor ponerle algo como <code>Entero</code>. Lo podemos hacer con una sentencia <code>using</code> de la siguiente manera:
```csharp  
using Entero = System.Int32;
```  
Y después usarlo de la siguiente manera:
```csharp  
Entero uno = 1;
Entero dos = 2;
Console.WriteLine(uno + dos); // Imprimirá 3
```  
Pero bueno, no creo que cambiarle el nombre a un entero les haga mucho sentido, así que trasladémoslo a una aplicación real. Supongamos que tenemos una aplicación que separa el modelo del acceso a datos, dentro de ella también tenemos dos clases llamadas <code>Person</code>, una dentro del namespace <code>MyApp.DataAccess.Entities</code> y otra dentro de <code>MyApp.ViewModels.Editable</code>. Para que la aplicación funcione hay un punto en el que ambas clases deben coincidir para que *mapear* de una a otra. Sin alias tendríamos que usar el nombre completo de las clases: 
```csharp  
public static MyApp.DataAccess.Entities.Person MapPerson(MyApp.ViewModels.Editable.Person person)
{
	// ...
```  
Ahora, usando alias reemplazaremos los nombres por algo más corto (y probablemente amigable):  
```csharp  
using EntityPerson = MyApp.DataAccess.Entities.Person;
using EditablePerson = MyApp.ViewModels.Editable.Person;
// ... ... ...
public static EntityPerson MapPerson(EditablePerson person)
{
	// ...
```  
### Para nombres de espacio  
Hay que aceptarlo, cambiar el nombre de los tipos puede resultar un poco confuso, pero C# también nos ofrece la posibilidad de ponerle un alias a los *namespaces* y se hace de la misma manera que para los tipos de dato:  
```csharp  
using Ents = MyApp.DataAccess.Entities;
using Edt = MyApp.ViewModels.Editable;
// ... ... ...
public static Ents.Person MapPerson(Edt.Person person)
{
	// ...
```  
### Gran, gran desventaja  
Dejé lo peor para el final: Los alias creados por nosotros solamente son válidos dentro del bloque de código en el que los definamos, ya sea dentro de un <code>namespace</code> o dentro de un archivo completo, lo cual significa que tenemos que repetirlo para cada archivo en el que lo usemos. Sería muy útil poderlos definir una sola vez a nivel global y después poder usarlas en toda nuestra aplicación.

#### Mi consejo 
Estos alias existen, pero yo procuro no usarlas salvo en casos extremos y es que si no se usan con moderación pueden complicar mucho la lectura del código. 

