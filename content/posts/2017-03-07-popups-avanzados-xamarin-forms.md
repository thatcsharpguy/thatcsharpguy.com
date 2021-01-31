---
layout: post
title: Popups avanzados en Xamarin.Forms
date: 2017-03-07 19:00:01
author: Antonio Feregrino
summary: Añade popups más avanzados a tus apps hechas con Xamarin.Forms usando este NuGet, tan sencillo como crear una pagina nueva y agregarle los controles que quieres mostrar.
featured_image: featured.jpg
images_folder: /xamarin-forms/popups/
github: https://github.com/ThatCSharpGuy/PopupSample
lang: es
tags: NuGetRecomendado, XamarinForms, Xamarin
featured_tag: NuGetRecomendado
---

En un post anterior les había hablado sobre UserDialogs que nos ayuda a mostrar mensajes a los usuarios, sin embargo, mientras que esa es una herramienta poderosa, es un poco limitada en términos de lo que podemos lograr debido a que tiene pocas opciones para  usar. Y precisamente por eso me puse a la búsqueda de otra solución que ofreciera más posibilidades de personalización.  

Fue así como me encontré con el *Popup Page Plugin for Xamarin Forms*, del cual les hablaré en este post.  

Lo primero que hay que tener en cuenta es que **cada popup que necesitemos en nuestra aplicación debe ser del tipo `Rg.Plugins.Popup.Pages.PopupPage`**, es decir, no es muy distinta de crear una página normal para Xamarin.Forms, puedes definirla en Xaml o usando código. Para este ejemplo usaré solamente Xaml.

## Creando popups

### Popup básico   
En el siguiente Xaml puedes ver la definición de una *popup page* muy sencilla que solo contiene un `Label`:

```xml  
<popup:PopupPage 
  xmlns="http://xamarin.com/schemas/2014/forms"
	xmlns:popup="clr-namespace:Rg.Plugins.Popup.Pages;assembly=Rg.Plugins.Popup"
	xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
	x:Class="PopupSample.Pages.BasicPopup"
	Padding="20">
  <Label HorizontalTextAlignment="Center" FontSize="Large" 
	Text="Hello basic popup!"></Label>
</popup:PopupPage>
```  

Lo básico aquí es la definición del namespace `popup` y el uso de la `PopupPage`. Luego, para mostrar la página hay que hacer uso del servicio de navegación `PopupNavigation` incluido en esta librería:

```csharp  
await PopupNavigation.PushAsync(new BasicPopup());
```  

El resultado es algo como esto:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/popups/basicpop.jpg" title="Basic pop" />

No se ve mucho como un *popup*, ¿verdad? ahora mismo lo componemos.  

### *Popup* con estilo  
Una guía de diseño que me parece adecuada es colocar un `Frame` centrado en la pantalla y dentro de este colocar el contenido que queremos mostrar, algo como esto:  

```xml  
<popup:PopupPage 
	xmlns="http://xamarin.com/schemas/2014/forms"
	xmlns:popup="clr-namespace:Rg.Plugins.Popup.Pages;assembly=Rg.Plugins.Popup"
	xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
	x:Class="PopupSample.Pages.StilizedPopup">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center" Padding="0" Margin="0">
    <Frame Margin="30" Padding="10">
      <Label HorizontalTextAlignment="Center" FontSize="Large" Text="Hello stilized popup!"></Label>
    </Frame>
  </StackLayout>
</popup:PopupPage>
```  

Que resultará en algo así:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/popups/stilized.jpg" title="Basic pop" />

### Propiedades del *popup*
Por default el *popup* se cerrará cuando el usuario presione fuera del contenido que le estamos mostrando, pero en caso de que no desees este comportamiento basta con establecer la propiedad `CloseWhenBackgroundIsClicked` en `false`. Otra de las propiedades que se pueden cambiar es el color del fondo mediante la propiedad `BackgroundColor`, no siempre tiene que ser gris transparente. Y por último la que creo que la más atractiva de las propiedades: `Animation` que nos permite especificar la forma en que aparecerá y desaparecerá el control en la pantalla. Las posibilidades que tenemos son: *Scale*, *Move* y *Fade*: 

```csharp  
var propertiedPopup = new PropertiesPopup();

propertiedPopup.Animation = new ScaleAnimation();
propertiedPopup.BackgroundColor = Color.FromRgba(0, 0, 0, 0.75);
propertiedPopup.CloseWhenBackgroundIsClicked = true;

await PopupNavigation.PushAsync(propertiedPopup);
```  

Si estableces que el *popup* no debe cerrarse cuando el usuario de click en el fondo, no olvides proveerle una manera de volver al estado anterior, por ejemplo, un botón. Para quitar un popup basta con llamar al método `PopAsync` del servicio de navegación:  

```csharp  
private async void Button_OnClicked(object sender, EventArgs e)
{
    await PopupNavigation.PopAsync();
}
```  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/popups/complex.jpg" title="Basic pop" />

### Animaciones personalizadas  
Esta biblioteca es poderosa, como ya habrás visto, pero sus capacidades pueden ampliarse ya que permite introducir animaciones personalizadas o modificar algunas ya existentes, como es el caso del siguiente bloque de código:

```csharp  
var propertiedPopup = new PropertiesPopup();

var scaleAnimation = new ScaleAnimation
{
    PositionIn = MoveAnimationOptions.Top,
    PositionOut = MoveAnimationOptions.Bottom,
    ScaleIn = 1.2,
    ScaleOut = 0.8,
    DurationIn = 400,
    DurationOut = 800,
    EasingIn = Easing.BounceIn,
    EasingOut = Easing.CubicOut,
    HasBackgroundAnimation = false
};

propertiedPopup.Animation = scaleAnimation;
await PopupNavigation.PushAsync(propertiedPopup);
```  

Que generará una animación así:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/popups/anim.gif" title="Custom animation" />

### Mi uso  
A final de cuentas yo la usé para crear este *popup* en una de las apps que estoy haciendo, quedó algo como esto:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/popups/custom_layout.jpg" title="Basic pop" />


## Uso  
Para comenzar a usar estos maravillosos *popups* tienes que instalar el <a href="https://www.nuget.org/packages/Rg.Plugins.Popup/" target="_blank">paquete de NuGet</a> en tus tres proyectos.  

```  
PM> Install-Package PlatformTabbedPage
```  

No olvides que esta biblioteca también es *open source* y puedes encontrar su código fuente en <a href="https://github.com/rotorgames/Rg.Plugins.Popup" target="_blank">GitHub</a>. Por cierto, no te olvides de descargar el código fuente de este post, <a href="https://github.com/ThatCSharpGuy/PopupSample" target="_blank">también en GitHub</a>.