---
layout: post
title: Xamarin.Forms para macOS
date: 2017-04-18 19:00:01
author: Antonio Feregrino
summary: Por fin, Xamarin.Forms llega a macOS. Reusa tu código al máximo y haz que tus apps lleguen a todo el mundo.
featured_image: featured.jpg
images_folder: /xamarin-forms/mac/
github: https://github.com/ThatCSharpGuy/MacForms
lang: es
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

No sé en realidad cuántos de ustedes que están leyendo este post están interesados en crear aplicaciones para Mac (yo espero que muchos), pero nunca está de más poder atacar más mercados con nuestras apps. Y qué mejor que hacerlo con el código que tenemos ya escrito para nuestras apps de iOS y Android.  


Pues bien, por fin es "oficial", después de que por mucho tiempo se estuvo trabajando en una versión de  Xamarin.Forms que funcionara para el sistema operativo de Apple, por fin tenemos disponible una versión que podemos probar sin necesidad de compilarla nosotros mismos.  

La forma de hacerlo es sencilla, y lo será más aún si ya sabes <a href="http://thatcsharpguy.com/post/anatomia-app-xamarin-forms/" target="_blank">cuál es la anatomía básica</a> de una app hecha con Xamarin, pero si no, no te preocupes, no es necesario que lo sepas. Solo tienes que seguir estos pasos:

Solo una cosa antes de comenzar, recuerda que esta no es todavía una versión estable, así que tal vez quieras crear una nueva rama en tu <a href="http://thatcsharpguy.com/tv/control-versiones/" target="_blank">control de versiones</a>

### 1. Añadir un proyecto de macOS  

El primer paso es agregar un proyecto del tipo Cocoa App:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/mac/createproject.jpg" title="Create project" />

### 2. Agrega Xamarin.Forms

Agrega el paquete de NuGet Xamarin.Forms, recuerda que es la versión `2.3.5.233-pre1`, no olvides actualizar las versiones en los otros proyectos también.   

### 3. Referencia desde el proyecto de Mac al de Forms

Agrega una referencia de tu proyecto de Mac al proyecto principal de tu aplicación de Forms.  

### 4. Edita el archivo Info.plist

Hay que quitar la referencia al *Storyboard* por default, esto se logra borrando la entrada `NSMainStoryboardFile` en el archivo `Info.plist`, puedes usar el editor gráfico en la pestaña Avanzado.  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/mac/infoplist.jpg" title="Archivo Info.plist" />

### 5. Edita el archivo Main.cs  

Hay que indicarle a la aplicación que su delegado es el archivo `AppDelegate`:  

```csharp  
	static void Main(string[] args)
	{
		NSApplication.Init();
		NSApplication.SharedApplication.Delegate = new AppDelegate();
		NSApplication.Main(args);
	}
```  

### 6. Edita el archivo AppDelegate.cs  

La primera modificación es cambiar la clase base de `NSApplicationDelegate` a `FormsApplicationDelegate`.   

El segundo cambio es sobrescribir la propiedad `NSWindow`, vamos a hacer una propiedad con un campo de respaldo:   

```csharp  
NSWindow _window;
public override NSWindow MainWindow
{
    get { return _window; }
}
```  

Ahora, en el constructor de la clase vamos a inicializar la `NSWindow` anterior. Básicamente se trata de establecer las opciones para la ventana en la que se mostrará nuestra aplicación: 

```csharp  
public AppDelegate()
{
    var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

    var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
    _window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
    _window.Title = "Who is in space?";
    _window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```  

Y por último, como en iOS, hay que sobrescribir el método `DidFinishLaunching` para que cargue la aplicación de Forms.

```csharp  
public override void DidFinishLaunching(NSNotification notification)
{
    Forms.Init();
    LoadApplication(new App());
    base.DidFinishLaunching(notification);
}
```  

## Esto es importante

Antes de terminar es importante mencionar que puede ser muy probable que no puedas restaurar muchos de los paquetes de NuGet porque deben ser recompilados estableciendo a Mac como uno de las versiones soportadas, así que no te preocupes, en el futuro seguramente serán soportadas. Puedes comunicarte con el desarrollador del paquete en cuestión para informarle que tal vez quiera actualizarlo para hacerlo compatible.  

Si quieres, puedes <a href="https://github.com/ThatCSharpGuy/MacForms" target="_blank">descargar esta app</a> que está lista para ejecutarse. Esta app te dirá los nombres de las personas que se encuentran en el espacio, así como la ubicación de la Estación Espacial Internacional, busca el <a href="https://github.com/ThatCSharpGuy/MacForms" target="_blank">código en GitHub</a>.  