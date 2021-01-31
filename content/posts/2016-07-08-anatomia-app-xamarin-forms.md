---
layout: post
title: Xamarin.Forms, anatomía de una app
date: 2016-07-04 21:00:00
author: Antonio Feregrino
categories: c-sharp
summary: Las partes que componen una aplicación hecha con Forms son dos&ellip; el núcleo o shared code y los proyectos clientes móviles que consumen dicho núcleo.
images_folder: /xamarin-forms/arch/
featured_image: featured.jpg
lang: es
github: https://github.com/ThatCSharpGuy/xfmvvm
tags: Xamarin, Events, XamarinForms
featured_tag: XamarinForms
---

<div class="video-wrapper">
<iframe src='https://onedrive.live.com/embed?cid=05D7523CBBAF0419&resid=5D7523CBBAF0419%21128893&authkey=AIJIECFXvuJszW0&em=2&wdAr=1.7777777777777777' width='350px' height='221px' frameborder='0'>Esto es un documento de <a target='_blank' href='https://office.com'>Microsoft Office</a> incrustado con tecnología de <a target='_blank' href='https://office.com/webapps'>Office Online</a>.</iframe>
</div>
 
<br />

En el blog he hablado mucho de Xamarin.Forms, pero nunca he explicado cómo es que funciona una app hecha con Forms... pues bien, en este post lo explico. Para este post estaré usando la aplicación <a href="../mvvm-xamarin-forms" target="_blank" rel="nofollow">Mvvmdex que expliqué en un post pasado</a>.  

Comenzando por la estructura en componentes de la aplicación, en el siguiente diagrama se muestra a grandes rasgos las partes que componen una aplicación hecha con Forms, en el diagrama podrás ver que una app se compone de dos tipos de proyecto: el núcleo (en el diagrama tiene la etiqueta *Shared Code*) y los clientes (iOS, Android y UWP):

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/main.png" title="Arquitectura" />

## Núcleo

En el núcleo es en donde ocurre la mayor parte del trabajo en una app con Forms ya que contiene gran parte de lo que la hace funcionar, incluyendo la interfaz gráfica. Es importante señalar que el hecho de que en el diagrama esté todo agrupado, no necesariamente tiene que ser así, podemos dividir el núcleo en tantos proyectos como sea necesario y conveniente para nuestro desarrollo. Por ejemplo, en mi app Mvvmdex, el núcleo está dividido en tres: Models, ViewModels y Views:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/full.png" title="Arquitectura" />

Este tipo de proyecto es usualmente una <a href="../que-son-portable-class-library" target="_blank" rel="nofollow">librería de clases portable</a>, aunque también puede ser un proyecto compartido. Sin importar qué tipo de proyecto sea, debes tener en cuenta las partes fundamentales en el código del núcleo, las cuales son:  

 - Que exista una clase derivada de `Xamarin.Forms.Application`. En el caso de la Mvvmdex esta se encuentra en el archivo `App.xaml.cs`. Esta clase puede ser de solo código o depender de un archivo Xaml asociado.

 - Dentro del constructor de dicha clase establecer la página inicial de nuestra aplicación a través de la propiedad `MainPage`  
 	En el template por default la página es creada directamente en el constructor, en mi app, la pagina principal está en un archivo separado.  

Así es como se ve en el sistema de archivos y en el explorador de soluciones:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/xf-files.jpg" title=""Xamarin.Forms en sistema de archivos"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/views.png" title=""Xamarin.Forms en el explorador de soluciones"" />
</div>  
</div>

## Clientes  

Los proyectos clientes son aquellos que "consumen" el núcleo, estos tienden a requerir menos trabajo ya que su expresión más sencilla, su única labor es cargar el núcleo usando código específico de la plataforma. En general la forma de ejecutar la aplicación es similar en todas las plataformas soportadas (Android, iOS y Windows): Primero se carga el inicializa el mecanismo de Xamarin.Forms y después se carga el núcleo de nuestra app. A continuación los detalles:  


### iOS  

En iOS hay tres tareas a realizar:  
  
   - Que nuestro `AppDelegate` derive de `FormsApplicationDelegate`  
   - Inicializar Forms llamando a `Forms.Init();`, esto debe ser en el método sobreescrito `FinishedLaunching`
   - Finalmente se carga la aplicación con el método `LoadApplication`, pasándole como parámetro una instancia de nuestra `App`  

Así es como se ve en el sistema de archivos y en el explorador de soluciones:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/ios-files.jpg" title="iOS en sistema de archivos" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/ios.png" title="iOS en el explorador de soluciones" />
</div>  
</div>  
  
### Android  

En esta plataforma de igual manera son tres tareas:  
  
   - Que la "`MainActivity`" derive de `FormsAppCompatActivity` (o de `FormsApplicationActivity`)
   - Inicializar Forms con `Forms.Init(this, bundle);`, pasandole como parámetros la *activity* en cuestión y su *bundle*, la inicialización debe suceder en el método `OnCreate`
   - Finalmente se carga la aplicación con el método `LoadApplication`, pasándole como parámetro una instancia de nuestra `App`  

Así es como se ve en el sistema de archivos y en el explorador de soluciones:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/android-files.jpg" title="Android en sistema de archivos" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/droid.png" title="Android en el explorador de soluciones" />
</div>  
</div>  
  
### Windows  

Trabajar con Windows es un poco "complicado", ya que requiere modificar dos archivos:  
  
   - En la clase `App` del proyecto de Windows inicializar Forms con `Forms.Init(e);`, es importante pasarle los argumentos del método como parámetros. Esto debe ocurrir dentro del método `OnLaunched`.
   - Ahora, en el archivo `MainPage.xaml`, debemos cambiar el tipo de la página de `Page` a `WindowsPage`, para esto es necesario añadir un *xml namespace*   
   - Finalmente, como con los anteriores, se carga la aplicación con el método `LoadApplication` (pasándole como parámetro una instancia de nuestra `App`) dentro del constructor de la `MainPage`  

Así es como se ve en el sistema de archivos y en el explorador de soluciones:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/uwp-files.jpg" title="Windows en sistema de archivos" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/arch/uwp.jpg" title="Windows en el explorador de soluciones" />
</div>  
</div>  
   
## Conclusión  

Tal vez esto podrá parecerte muy... innecesario, ya que los templates que trae Xamarin hacen esta tarea por nosotros, sin embargo, yo creo que nunca está de más saber cómo es que funcionan las cosas, además de que tu aplicación exija que este proceso de ejecución de la aplicación se realice de una manera distinta a la forma por default de Xamarin.Forms, por ejemplo <a href="../opening-files-xamarin-forms">para recibir archivos</a> en una aplicación. 

