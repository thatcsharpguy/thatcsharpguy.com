---
title:  Xamarin.Forms
slug: xamarin-forms
date:  2016-05-26 22:00:00
youtube_id: UaUsf-y0GfY
images_folder:  /tv/xamarinforms/
categories:  c-sharp
summary:  Con Xamarin.Forms munca había sido tan fácil crear aplicaciones multiplataforma compartiendo la mayoría del código entre ellas, incluyendo la interfaz gráfica. En este video te explico de qué se trata.
featured_image:  featured.jpg
tags:  Meta, YouTube, Xamarin, XamarinForms
featured_tag:  Xamarin
layout: video
---

En un <a href="../xamarin">video anterior les había ya hablado sobre Xamarin</a>, que es una tecnología que nos permite hacer aplicaciones multiplataforma. En aquel video les conté que de entrada, con Xamarin.iOS y Xamarin.Android debíamos seguir haciendo las interfaces gráficas como tradicionalmente se hacen, es decir, una interfaz para iOS, una para Android y otra para Windows pero también adelanté que existía una herramienta llamada Xamarin.Forms para compartir incluso el código de la interfaz.  

Xamarin.Forms es una biblioteca, <a href="https://www.nuget.org/packages/Xamarin.Forms/" target="_blank" rel="nofollow">que podemos descargar de NuGet</a>, que expone un conjunto de abstracciones de algunos elementos de interfaz gráfica comúnmente usados en plataformas como Android, iOS y el ya viejo, Windows Phone, recientemente, Universal Windows Apps.  

Estas abstracciones se convertirán en tiempo de ejecución a controles nativos a cada plataforma, permitiendo así definir la interfaz una sola vez y que nuestra aplicación se muestre en el dispositivo como si hubiera sido escrita usando los controles definidos para cada plataforma.

Dentro de estas abstracciones de controles tenemos:

 - Páginas (Pages) que son elementos sobre los cuales se colocan los controles de interfaz gráfica, se podría decir que son parecidas a las `Activities` en Android o a los `ViewControllers` en iOS, pero solo se parecen, no cumplen las mismas funciones. Forms nos da algunas páginas predefinidas como: 
	 - `ContentPage` que es la página más básica
	 - `MasterDetailPage` que es una página que permite crear un efecto de menú en tu aplicación
	 - `TabbedPage` que es una página con pestañas
     
En esta imagen puedes ver todas:
     
<img src="https://thatcsharpguy.github.io/postimages/tv/xamarinforms/pages.png" title=""Xamarin.Forms stock pages"" />
     
 - Contenedores (Layouts) que nos permiten acomodar los elementos de interfaz gráfica dentro de una página, de igual manera, Forms nos da algunos `Layouts` comunes como:
	 - `StackLayout` para apilar los controles, uno sobre otro
	 - `ScrollView` que permite acceder a elementos que están fuera de la pantalla del dispositivo
	 - `Grid` que nos deja acomodar los elementos de interfaz en filas y columnas
	 - `RelativeLayout` para colocar los elementos con posiciones relativas entre ellos

En fin, hay varios más
     
<img src="https://thatcsharpguy.github.io/postimages/tv/xamarinforms/layouts.png" title=""Xamarin.Forms stock layouts"" />

 - Controles (Views) son elementos visuales, y en donde realmente se "nota" más, el poder de Forms. Estos elementos visuales son los que reciben directamente la interacción del usuario, por ejemplo, en Forms tenemos:
	 - `Button`, que no necesita mucha explicaciòn
	 - `Label` para mostrar texto
	 - `Entry` que permite ingresar texto
	 - `Image` que es un control que permite mostrar una imagen
	 - `ListView` usado para mostrar elementos dentro de celdas en forma de lista
	 - `TableView` muy similar a `ListView`
	 - `DatePicker` para seleccionar fecha
	 - En fin, hay muchos más.
- Celdas (Cells) que como ya lo mencioné, se usan junto con las `ListView` o `TableView` para mostrar datos, en esta categoría hay celdas que nos permiten mostrar texto, texto e imágenes o controles como un Switch o una caja de texto.  

<img src="https://thatcsharpguy.github.io/postimages/tv/xamarinforms/controls.png" title=""Xamarin.Forms stock layouts"" />

Xamarin.Forms funciona así: las abstracciones de controles se transforman en tiempo de ejecución a controles nativos, en cada plataforma hay un conjunto de *Renderers* que toman la abstracción y la convierten al control nativo. De tal manera que cuando nosotros usamos un control, por ejemplo digamos `Entry` (para recibir texto del usuario), estos `Renderers` lo convertirán en Android a un `EditText`, en iOS a un `UITextView` y en Windows a un `TextBox`:  

<img src="https://thatcsharpguy.github.io/postimages/tv/xamarinforms/entry_rendering.png" title="Rendering" />

Al ser abstracciones, es muy posible que muchas de las propiedades que son específicas a cada plataforma no estén disponibles en Xamarin.Forms ya que se trata de ofrecer un conjunto de propiedades común a todas las plataformas. Esto es una limitante, ya que con Forms no podemos alcanzar un alto nivel de personalización en nuestras apps.

### Custom renderers

Pero no te desanimes, así como Forms hace uso de los renderers, tu también los puedes usar para especificar cómo quieres que se muestren los controles en cada plataforma o, inclusive, para añadir controles que no están disponibles a través de la API de Forms como <a href="https://www.nuget.org/packages/Messier16.…" target="_blank" rel="nofollow">el checkbox que hice</a>. Si quieres saber más, <a href="https://developer.xamarin.com/guides/xamarin-forms/custom-renderer/introduction/" target="_blank" rel="nofollow">puedes checar la documentación</a>.

### Lenguaje de interfaz
  
Otra de las dudas más comunes es sobre el cómo escribimos la interfaz usando Xamarin.Forms, y pues las respuestas son dos:  

<img src="https://thatcsharpguy.github.io/postimages/tv/xamarinforms/language.png" title=""Lenguaje de interfaz gráfica"" />

Sí, XAML y código C#. Yo preiero el código, aunque si tienes muchos controles, la cosa se puede tornar bastante fea si no tienes buena organización.

### Código de cada plataforma

A pesar de usar Forms, nunca nos vamos a liberar de tener que ver (o usar) código dentro de cada plataforma, ya que como he mencionado antes, esta herramienta es simplemente una librería que es ejecutada cuando nuestra aplicación corre. Esto se ve cuando creamos un proyecto nuevo, en Android, dentro del archivo `MainActivity` y en iOS dentro de `AppDelegate` se carga la aplicación hecha con forms.

### Code behind y MVVM

Si has estado desarrollando en Windows Forms o con Java Swing es muy probable que conozcas el concepto de *code behind*, ese concepto se sigue empleando en Xamarin.Forms pero no se hace mucho énfasis en él como si se hace en patrón MVVM, que es un patrón que puede facilitar el desarrollo de aplicaciones de gran tamaño y complejidad.

### Xamarin tradicional o Xamarin.Forms
Mucha gente se puede llegar a hacer esta pregunta: ¿Para qué siguen existiendo Xamarin.iOS y Xamarin.Android si ya tenemos Forms? y pues la razón está definida dentro de lo que ofrece el mismo Forms: la poca personalización que se puede conseguir. En general, se recomienda ir por la vía tradicional si:

 - Tu aplicación hace uso intensivo de las APIs nativas de cada plataforma (cosas como cámara, acelerómetro y por el estilo)
 - Tu aplicación hace uso de una interfaz personalizada y distinta de la interfaz nativa
 - Quieres usar el potencial del desempeño nativo
 - Estás haciendo un juego
 
 Por otro lado, usa Forms si:
 
 - Tu app no usa muchas APIs de acceso a cámara, acelerómetro o cosas específicas del dispositivo
 - La personalización de la interfaz no es tan importante
 - Ya estás familiarizado con XAML o apenas estás teniendo contacto con el desarrollo móvil
 
Te invito a revisar las etiquetas <a href="../../tag/XamarinForms/">#XamarinForms</a> y <a href="../../tag/Xamarin/">#Xamarin</a> si quieres saber más sobre el desarrollo móvil con esta tecnología. No olvides suscribirte y compartir el video.