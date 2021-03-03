---
layout: post
title: Xamarin.Android vs Android tradicional (con Java)
slug: xamarinandroid-vs-android-tradicional-con-java
date: 2016-05-09 21:00:00
author: Antonio Feregrino
featured_image: java-c-sharp.png
categories: xamarin
lang: es
summary: En cuanto a la famosa clase R, en Xamarin.Android se llama Resource y su contenido es similar al que contiene en la tradicional, como podrás ver en el código más adelante.
images_folder: /xamarin-android/
github: https://github.com/ThatCSharpGuy/silo-xamarin-comparation
tweet_id: 729874348660555777
tags: VisualStudio, Xamarin
featured_tag: Xamarin
---

Al igual que  <a href="../xamarin-ios-vs-traditional">como lo hice con iOS</a>	, en este post te mostraré algunas de las diferencias y similitudes entre el desarrollo con Java y C# para Android. Para este post cree una app que compara dos cadenas y calcula su <a href="https://es.wikipedia.org/wiki/Distancia_de_Levenshtein" target="_blank" rel="nofollow">distancia</a> entre ellas, hice dos versiones una tradicional y una con Xamarin.Android. Esta es la app finalizada:
 
<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/app.gif" title="App terminada" />
 
 Yo incursioné por primera vez en el desarrollo móvil tomando un pequeño curso de Android y no mucho ha cambiado desde entonces, para este post usé

### Estructura del proyecto
<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/comparison.png" title="Comparison project structure" />

<ol>
	<li><b>Proyecto móvil</b>. El concepto de proyecto es el mismo, en ambos casos el un proyecto se refiere a una sola aplicación.</li>
   	<li><b>AndroidManifest.xml</b>. Este archivo en el que se define gran parte de la configuración del proyecto se mantiene idéntico en ambas opciones de desarrollo. Sin embargo, cuando usamos Xamarin podemos trasladar parte de esta configuración al código, pero más adelante hablaré de eso.</li>
  	<li><b>Recursos</b>. Los recursos se siguen organizando de la misma manera (en carpetas dependiendo de su resolución/tamaño), solo que mientras que usando Xamarin la carpeta se llama <code>Resources</code>, en el tradicional se llama <code>res</code>.</li>
   	<li><b>Layouts</b>. En el caso de esta app, tanto el archivo <code>Main.axml</code> como el <code>activity_main.xml</code> tienen más o menos el mismo contenido y de hecho son compatibles entre ellos. La diferencia en las extensiones es para que Visual Studio lo reconozca como un archivo de interfaz gráfica para Android y pueda dar usando Intellisense.</li>
   	<li><b>Drawables por resolución</b>. A diferencia de iOS, en donde se distingue un recurso de otro mediante los sufijos `@x</code>, en Android estos deben ir en carpetas separadas totalmente, y en Xamarin no es la excepción.</li>  
   	<li><b>Drawables en xml</b>. En este caso el archivo <code>empty_divider.xml</code> tiene el mismo contenido, para esta app el archivo contiene un divisor transparente para el <code>StackView</code>.</li>  
   	<li><b>Recursos en xml</b>. Cualquier recurso como <code>strings</code>, <code>colors</code>, <code>values</code> es empleado de la misma manera.</li>  
   	<li><b>Biblioteca de lógica externa</b>. En ambos casos la lógica de nuestra aplicación puede ser separada en un proyecto externo, en ambos casos, una librería. Cabe mencionar que usando C# creamos una biblioteca de clases portable y compartir este código con apps de iOS u otras plataformas.</li>
   	<li><b>Código en general</b>. El código se guarda en clases, de estas diferencias hablaré más adelante.</li>
</ol>

### Recursos
Como ya mencioné, los recursos se manejan de manera similar, algo que me agradó de Android Studio es cómo muestra los recursos: fuera de las carpetas y con una anotación indicando a qué resolución pertenecen.  

En cuanto a la famosa clase <code>R</code>, en Xamarin.Android se llama <code>Resource</code> y su contenido es similar al que contiene en la tradicional, como podrás ver en el código más adelante.

### El código

#### MainActivity

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/MainActivity.png" title="MainActivity" />

Desde aquí se revelan algunas de las diferencias entre el código de C# y Java, por ejemplo, podemos ver que <code>using</code> ≈ <code>import</code>, o que el <code>namespace</code> es un equivalente de <code>package</code> y que las clases se llaman igual.

Algo importante a destacar es el código `[Activity(MainLauncher = true)]` que cumple la función de indicarle al manifiesto cuál es el punto de entrada a nuestra app (en <a href="../opening-files-xamarin-forms/#android">este post</a> muestro las equivalencias entre el manifiesto y el atributo <code>Activity</code>).

#### OnCreate

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/OnCreate.png" title="OnCreate" />

En cuanto al método <code>OnCreate</code> y la declaración <code>View</code>s, las diferencias son mínimas, en C# para hacer referencia a la clase de la que se hereda se usa la palabra <code>base</code> en lugar de <code>super</code>. Por convención los métodos comienzan con mayúscula.

Aquí es donde se ven las similitudes entre la clase <code>R</code> y <code>Resource</code> para acceder a los recursos.


##### FindViewById

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/FindViewById.png" title="FindViewById" />

Una vez creada la vista podemos acceder a los controles y referenciarlos desde la clase <code>Activity</code>, para esto usamos el método <code>FindViewById</code> y mientras que con Xamarin tenemos la opción de hacer el *cast* nosotros (como en Java), también podemos usar el método genérico <code>FindViewById<T>` que hará el *cast* por nosotros.

#### Métodos y manejadores de evento

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/EventAssign.png" title="Asignación de eventos" />

En cuanto a los métodos, la declaración es casi idéntica (y más con Java 8) ya que mientras que en C# se usa una expresión lambda en la que se define el comportamiento del botón, en Java se utiliza una clase anónima para realizar esta acción.

### Código en general

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/Code.png" title=""Código Java & C#"" />

En cuanto al código en general, me atrevería a decir que la traducción entre C# y Java no es muy complicada... son como hermanos, en realidad una de las más "grandes" diferencias es que en Java tenemos únicamente métodos para acceder a las propiedades de un objeto como es el caso de <code>length()`.

## Interfaz gráfica

El editor de interfaces para Android que contiene Visual Studio o Xamarin Studio es idéntico al disponible en los editores de Android Studio o en Eclipse. Además de la vista "en vivo" del diseño también ambos nos dan acceso al xml del archivo.

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android/Designer.png" title="Editor de interfaces" />

### En conclusión

A diferencia de <a href="../xamarin-ios-vs-traditional">C# y Swift</a>, las diferencias con Java no son notorias y creo que bien valdría la pena comenzar a hacer tu siguiente app con Xamarin para así poderla llevar a iOS sin mayor problema.

Recuerda que el código de las apps tanto de C# y Java están <a href="https://github.com/ThatCSharpGuy/silo-xamarin-comparation" target="_blank" rel="nofollow">disponibles en GitHub</a> par que las revises, si tienes alguna duda, no dudes en contactarme, al final del post están los enlaces necesarios.

