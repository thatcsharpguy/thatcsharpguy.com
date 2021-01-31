---
layout: post
title: Xamarin.iOS vs iOS tradicional (con Swift)
date: 2016-05-02 21:00:00
author: Antonio Feregrino
featured_image: swift-c-sharp.png
categories: xamarin
lang: es
summary: Una de las diferencias más notorias es la asignación de manejadores de evento, fuera de eso la transición entre un lenguaje y otro es sencilla.
images_folder: /xamarin-ios/
github: https://github.com/ThatCSharpGuy/silo-xamarin-comparation
tags: VisualStudio, Xamarin
featured_tag: Xamarin
---

En este post te mostraré algunas de las diferencias que existen entre usar Swift y C# para desarrollar apps en iOS. Para este post cree una app que compara dos cadenas y calcula su <a href="https://es.wikipedia.org/wiki/Distancia_de_Levenshtein" target="_blank" rel="nofollow">distancia</a> entre ellas, hice dos versiones una tradicional y una con Xamarin.iOS. Esta es la app finalizada:
 
<img src="/images/xamarin-ios__app.gif" title="App terminada" />
 
Voy a ser honesto, esta es la primera vez que uso Swift para hacer una app para iOS, sin embargo me basé por completo en la <a href="https://developer.apple.com/library/ios/referencelibrary/GettingStarted/DevelopiOSAppsSwift/index.html?utm_source=statuscode&utm_medium=email" target="_blank" rel="nofollow">documentación de Apple</a> para hacerla. En este caso usé Xamarin Studio 5.10.3 y Xcode 7.3

### Estructura del proyecto
<img src="/images/xamarin-ios__structure-comp.png" title="Comparison project structure" />

<ol>
	<li><b>Proyecto principal</b>. Todo empieza con un proyecto principal, ¿cierto? en ambas opciones existe este concepto, sólo que en Xamarin existen <a href="../organizacion-codigo-visual-studio">las soluciones</a> y en con Xcode es un único proyecto padre.</li>
   	<li><b>Proyecto de la aplicación</b>. en Xcode relacionamos código enfocado a una tarea mediante la creación de un <b>grupo</b>, en este caso para la app el grupo se llama "Xevensthein", mientras que con Xamarin o Visual Studio esta relación entre código se establece mediante la creación de un <b>proyecto</b>.</li>
  	<li><b>AppDelegate</b>. Esta clase es una de las esenciales, y como puedes ver, en ambas opciones sigue existiendo una está escrita en Swift y la otra en C#, más adelante mostraré las diferencias en el código.</li>
   	<li><b>Main.storyboard</b>. La interfaz gráfica se define de la misma manera para cualquiera de las dos opciones, ya sea mediante un <i>storyboard</i> o un archivo <i>.xib</i>.</li>
   	<li><b>ViewController</b>. Así mismo, relacionado a la interfaz y para dotar de comportamiento a nuestra app, también tenemos una clase llamada <i>ViewController</i>.</li>
   	<li><b>LaunchScreen</b>. Al ser nativas ambas, podemos usar un archivo para definir la pantalla que se muestra mientras nuestra app carga, en la plantilla de Xamarin Studio está definida en un <i>.xib</i> pero podríamos cambiarla por un <i>.storyboard</i> sin complicaciones.</li>  
	<li><b>Recursos</b>. Podemos usar recursos dentro de nuestra app, ya sea empaquetados en <i>Xcode assets catalogs</i> o como archivos individuales.</li>
	<li><b>Lógica de la aplicación</b>. Si deseamos implementar lógica aparte en nuestra app, usando Swift podemos crear un <i>framework</i> y referenciarlo desde el proyecto de la aplicación, mientras que con C# podemos crear una <a href="../que-son-portable-class-library">biblioteca de clases</a>, y lo mejor de todo es que esta biblioteca la podemos compartir con otras apps, como una hecha para Android, Windows 10 o inclusive una Raspberry PI.</li>
	<li><b>Clases</b>. Los <i>frameworks</i> o bibliotecas de clases, contienen más archivos que componen la lógica de la aplicación, como dije, más adelante mostraré las diferencias en el código.</li>
</ol>

### Recursos
Como ya mencioné, los recursos se manejan de manera similar, podemos importar archivos directamente a un catálogo de imágenes o añadirlos a la carpeta Resources del proyecto.

### El código

#### AppDelegate

<img src="/images/xamarin-ios__appdelegate.png" title="AppDelegate" />

La clase AppDelegate es la encargada de recibir mensajes del sistema operativo y hacer que tu app responda a ellos. Como puedes ver, las diferencias son únicamente en la sintaxis, ya que en código es exactamente idéntico.

#### ViewController

<img src="/images/xamarin-ios__outlets.png" title="ViewController" />

Los *ViewControllers* son clases asociadas a la interfaz gráfica, a partir de las cuales se les da comportamiento a las apps. En el ejemplo de código arriba se puede ver que existen referencias a elementos de interfaz gráfica como `UITextField` e `UIButton`. Estas referencias son generadas por el constructor de interfaces y es por eso que en C# tienen los decoradores `Outlet` y `GeneratedCode`.  

En C# además existe el concepto de <a href="https://msdn.microsoft.com/es-MX/library/wa80x488.aspx?f=255&MSPPError=-2147217396" target="_blank" rel="nofollow">clases parciales</a> y Xamarin.iOS se ayuda  de eso para separar la declaración de elementos de interfaz de la descripción del comportamiento.

##### ViewDidLoad

<img src="/images/xamarin-ios__view-didload.png" title="ViewDidLoad method" />

Dentro del *ViewController* existe un método llamado `ViewDidLoad`, el cual es el ideal para conectar los eventos y cualquier otra tarea relacionada con la vista. En este caso se asigna el *ViewController* como delegado de los `UITextField` y también se le asigna un manejador de evento al botón `ComparteButton`. 

En Swift se usa `addTarget` y una serie de argumentos, mientras que en C# basta usar el operador `+=`.

#### Métodos y manejadores de evento

<img src="/images/xamarin-ios__event-handlers.png" title="Methods and event handlers" />

En cuanto a los métodos, la declaración es también idéntica aún tomando en cuenta la diferencia en la sintaxis. El decorador `Foundation.Export` le indica al compilador a qué método de Swift hace referencia el método de C#, pero no te preocupes, tanto Xamarin Studio como Visual Studio te ayudarán a generar ese tipo de código si no tienes mucha experiencia con iOS.

### Código en general

<img src="/images/xamarin-ios__library.png" title=""Código Swift & C#"" />

En cuanto al código en general, me atrevería a decir que la traducción entre C# y Swift no es muy complicada, basta con encontrar las equivalencias entre ellos, así como definir si las equivalencias realmente son la forma óptima de realizar la tarea. En particular a mí se me hizo un poco complicado el entender cómo acceder a los caracteres de una cadena y cómo declarar un arreglo multidimensional.

(También hice una pequeña <a href="../c-sharp-obj-c">comparativa entre Objective-C y C#</a>). 

## Interfaz gráfica

Tanto Xcode como Xamarin/Visual Studio cuentan con un editor de interfaces gráficas, y como puedes observar en esta imagen, las diferencias en la superficie no son muy notorias entre ellos:

<img src="/images/xamarin-ios__editor-diff.png" title="Editor de interfaces" />

Sin embargo, y basado en mi poca experiencia, me atrevo a decir que el editor de XS o VS aún está muy lejos de la estabilidad y facilidad de uso del editor de Xcode y es por eso que, aunque estemos usando Xamarin.iOS, podemos usar el editor nativo (**recuerda que con Xamarin.iOS tu app es 100% nativa**) para crear la interfaz:

<img src="/images/xamarin-ios__interface-builder.png" title="Native interface builder" />

### En conclusión

La sintaxis es una de las más grandes diferencias, sin embargo, no es muy distinta ya que me parece que Swift y C# son parecidos. Una de las diferencias más notorias es la asignación de manejadores de evento, fuera de eso la transición entre un lenguaje y otro es sencilla. Para la construcción de interfaces sorprende la capacidad de poder usar el editor de Xcode para construir la interfaz, sin necesidad de mover una sola línea de código para que funcione en Xamarin.iOS.

Recuerda que el código de las apps tanto de C# y Swift están <a href="https://github.com/ThatCSharpGuy/silo-xamarin-comparation" target="_blank" rel="nofollow">disponibles en GitHub</a> par que las revises, si tienes alguna duda, no dudes en contactarme, al final del post están los enlaces necesarios.

