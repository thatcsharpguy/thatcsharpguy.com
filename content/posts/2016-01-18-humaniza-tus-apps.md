---
layout: post
title: Humaniza tus apps
date: 2016-01-18 19:00:00
author: Antonio Feregrino
excerpt: Dale un toque más humano a tus aplicaciones con esta pequeña pero útil librería de clases que te ayuda a presentar en un lenguaje más fluído los datos dentro de tu app al usuario final.
featured_image: featured_es.png
github: https://github.com/ThatCSharpGuy/humanize-post
lang: es
tweet_id: 689257483748151297
tags: NuGetRecomendado
featured_tag: NuGetRecomendado
---

Es un hecho que actualmente cuando una aplicación es lanzada al público no solo es necesario que funcione bien y haga lo que promete, sino que además de todo se vea bien y su interfaz de usuario sea agradable a la persona que la necesita.  

Hay muchas cosas que tenemos que hacer para que nuestra aplicación sea agradable al usuario, y una de ellas es hacer que la información que se le presenta esté en un lenguaje más natural, que no parezca generado por una computador (aunque sí lo sea). ¿A qué me refiero con esto? veamos un ejemplo común:

Supongamos que dentro de nuestras aplicaciones queremos mostrar información de un evento que se llevará a cabo, información que tenemos almacenada como `int`s, `DateTime`s y `Decimal`s, y a la hora de mostrarlo al usuario, en la interfaz de nuestra app ponemos algo como:  
  
*Número de asistentes: 3* o *Número de asistentes: 1*  
*El evento será el 20/01/2016*  
*La duración es de 13:00 a 16:00* 
  
Lo cual no está mal... pero no es muy natural que digamos, y es por eso que muchos optan por mostrar algo como:

*3 asisistente(s)* o *1 asistente(s)*    
*El evento será el Miércoles, Enero 201, 2016*  
*La duración es de 13:00 a 16:00* 

Que ya es un poco más "lenguaje humano" pero tiene ese horrible paréntesis al final, desde luego, pudimos haber evitado eso escribiendo nuestro código con una condicional para revisar la cantidad de asistentes antes de imprimirlo, pero sería tratar de reinventar la rueda. Es por eso que en este post hablaré de **Humanizer**, sus múltiples características y de como podemos pasar de la interfaz anterior a una como esta:  

*3 asistentes*  
*El evento será en 4 días*    
*Tiene una duración de 3 horas*  

### Instalando Humanizer  
Humanizer está en forma de [una Portable Class Library](/post/que-son-portable-class-library/), y disponible a través de NuGet, por lo que es sencillo instalarla en cualquier proyecto, desde una aplicación de consola (como la de este post) hasta una app de Xamarin.iOS. Basta con instalar el paquete `Humanizer.Core` usando la consola del administrador de paquetes:  

```  
PM> Install-Package Humanizer
```  

O a través de la interfaz gráfica en donde basta con buscar `Humanizer.Core` e instalarlo.

#### Soporte para otros idiomas
Originalmente Humanizer fue creado para funcionar con palabras del idioma inglés, pero afortunadamente gracias a la comunidad Open Source, se han añadido otros, como el español. Para añadirlo es necesario bajar un segundo paquete de NuGet con los recursos en español llamado `Humanizer.Core.es`.

Por default, para el idioma de los textos, Humanizer toma la cultura del hilo que lo ejecuta, para cambiarla puedes usar una línea de código:

```csharp  
Thread.CurrentThread.CurrentUICulture = new CultureInfo("es-ES");
```  

Si por las restricciones de tu aplicación no es posible hacer el cambio anterior, no te preocupes, generalmente los métodos que veremos a continuación permiten especificar la cultura en la que queremos que funcionen.

### La clase Event
Para este código de ejemplo usaré una clase llamada evento, que contiene dos `String` para el nombre y descripción del evento, dos `int` para el número de asistentes y para el número de evento, dos `DateTimes` para las fechas de inicio y fin del evento.  

Para este post, crearé un `Event` llamado `contest` con el que estaremos trabajando a lo largo de este post.

### Números 
Pasar de `8` a *"octavo"* no suena tan sencillo, sin embargo Humanizer lo hace, basta con llamar el método de extensión `ToOrdinalWords`, o también podemos usar el método `Ordinalize` para obtener una cadena más pequeña, como `8.º`:

```csharp  
contest.Number.ToOrdinalWords(GrammaticalGender.Masculine); // "octavo"
contest.Number.ToOrdinalWords(GrammaticalGender.Feminine); // "octava"
contest.Number.Ordinalize(GrammaticalGender.Neuter); // "8.º"
```  
  
Como podemos ver también nos da diversas opciones en cuanto a qué genero estamos expresando, o si queremos palabras o números ordinalizados.  
  
También es posible:  

 - Pasar de un número a una cadena, así entonces al llamar el método `ToWords` en un número, obtendremos su representación en palabras: `158` → "ciento cincuenta y ocho".
 - Pasar de un número arábigo a uno romano usando `ToRoman`: `158` → "CLVIII" y viceversa.
 - Manejar números como tamaños de archivo, pasar de megabytes a bits o de kilobytes a terabytes con facilidad
  
### Truncar cadenas 
Truncar cadenas es algo importante para cuando queremos mostrar una parte de un texto largo, ya sea para reducir costos en la transferencia de datos o para no llenar de texto la pantalla del usuario, eso de truncar cadenas suena a algo trivial que podemos implementar fácilmente nosotros mismos, sin embargo para truncar cadenas usando Humanizer basta llamar al método de extensión `Truncate`:  

```csharp  
contest.Description.Truncate(40); // 40 caracteres, terminando en '…' 
contest.Description.Truncate(40,"..."); // 40 caracteres, terminando en "..."
contest.Description.Truncate(7,Truncator.FixedNumberOfWords); // 7 palabras, terminando en '…'
```  

Se pueden hacer combinaciones como que sean 30 caracteres y termine en ":::", o que trunque el texto en 10 palabras comenzando por la derecha y que termine en "...".   

### Cuantificar
Bueno, no sé si "cuantificar" sea la palabra adecuada
Para evitar esos horribles paréntesis que indican que puede haber uno o más elementos de una categoría podemos usar el método `ToQuantity` que es aplicado sobre una cadena de texto y recibe un entero indicando la cantidad de elementos que queremos mostrar, de este modo ya no tendríamos que poner `asistente(s)`, sino únicamente hacer algo como esto:


```csharp  
"asistente".ToQuantity(contest.Attendants, ShowQuantityAs.Words);  // "cero asistentes".
"asistente".ToQuantity(contest.Attendants); // "0 asistentes"
"asistente".ToQuantity(1, ShowQuantityAs.None); // "asistente"
"asistente".ToQuantity(1, ShowQuantityAs.Numeric); // "1 asistente" 
```  

Desde luego, hay palabras que no serán directamente *pluralizables* o que simplemente no pueden ser cuantificadas, pero esas se pueden agregar previamente al vocabulario:

```csharp  
Vocabularies.Default.AddIrregular("feliz", "felices");

Vocabularies.Default.AddUncountable("lava");
```  

### Fechas e intervalos de tiempos  
<b>Importante:</b> al momento de hacer este post, parece que me encontré con un bug con la localización de las fechas, ya nos entrega el resultado en inglés, <a href="https://github.com/Humanizr/Humanizer/issues/513" target="_blank" rel="nofollow"><i>levanté un issue</i></a> en GitHub y trataré de encontrar el problema.
  
#### Fechas
En cuanto a las fechas hay muy poco margen de movimiento, una fecha es una fecha, pero qué mejor que darle una referencia más verbal al usuario, como "en dos semanas", "ayer" o "dentro de 10 dias" puede resultar mejor que únicamente mostrar el clásico "dd/MM/yyyy". 

En el ejemplo de los eventos, la propiedad `Start` es igual a `new DateTime(2016,2, 29, 10,0,0)` mientras que `End = new DateTime(2016,2,29,17,30,0)`, es por eso que al momento de utilizar `contest.Start.Humanize();` obtuve "en un mes".  
  
Pero para crear un ejemplo más claro:  

```csharp  
Console.WriteLine (DateTime.Now.Humanize(false)); // "Ahora"
Console.WriteLine (DateTime.UtcNow.Humanize()); // "Ahora"
Console.WriteLine (DateTime.UtcNow.AddDays(3).Humanize()); // "En tres días"
Console.WriteLine (DateTimeOffset.UtcNow.AddMonths(-10).Humanize()); // "Hace 10 meses"
```  

El método `Humanize` funciona tanto para tipos `DateTime` como para `DateTimeOffset` y entre sus sobrecargas podemos especificar si estamos manejando una fecha UTC, o contra qué fecha queremos comparar (por default usa `DateTime.UtcNow`).  

#### Intervalos de tiempo
En cuanto a intervalos de tiempo, bien podríamos mostrar las fechas separadas por un guión, pero al igual que con las fechas, tal vez sea mejor decir "una hora", "45 minutos" o "tres días".

Los eventos tienen una fecha de inicio y una de finalización, un escenario ideal para simplemente restar `Start` de `End` lo cual nos deja con un `TimeSpan`, sobre el cual podemos ejecutar el método de extensión `Humanize` para obtener una versión legible del intervalo:

```csharp  
(evt.End - evt.Start).Humanize(3); // "7 horas, 30 minutos"
```  

Otra vez, el método tiene muchas sobrecargas, entre las que podemos elegir con qué detalle queremos que se muestre la cadena, o cuál es la unidad máxima (o mínima) de tiempo que queremos tomar en cuenta. En el caso del ejemplo, coloqué un  `3` puesto que a lo mucho quiero que se muestren días, horas y minutos.  

#### Mejoras para los desarrolladores
Humanizer no solo son mejoras para el usuario final, sino también para nosotros los desarrolladores, para manejar fechas incluye una <a href="https://es.wikipedia.org/wiki/Interfaz_fluida" target="_blank" rel="nofollow">interfaz fluida</a> para hacer un poco más legible el código, para poner un ejemplo, tomaré este de la <a href="https://github.com/Humanizr/Humanizer#fluent-date" target="_blank" rel="nofollow">documentación oficial</a>:

```csharp  
DateTime.Now + 2.Days() + 3.Hours() - 5.Minutes();
```  

Es equivalente a 

```csharp  
DateTime.Now.AddDays(2).AddHours(3).AddMinutes(-5);
```  

### Otras características  
En este post dejé afuera la capacidad de Humanizer de trabajar con colecciones, enumeraciones y más posibilidades para trabajar con cadenas. Estas tres y otras características se pueden revisar en el <a href="https://github.com/Humanizr/Humanizer#table-of-contents" target="_blank" rel="nofollow">repositorio de Humanizer en GitHub</a>. 
