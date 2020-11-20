---
layout: post
title: UserDialogs en Xamarin Forms
date: 2017-01-17 19:00:01
author: Antonio Feregrino
summary: Usa esta librería de NuGet para interactuar con el usuario de tu aplicación de una forma más intuitiva, con mensajes de alerta, de error y de éxito. Consigue información a través de controles nativos como calendarios y selectores de tiempo.
featured_image: featured.jpg
images_folder: /xamarin-forms/userdialogs/
github: https://github.com/ThatCSharpGuy/UserDialogs-Sample
lang: es
tags: Xamarin, XamarinForms, NuGetRecomendado
featured_tag: XamarinForms
---

Creo que todos estamos de acuerdo en que Xamarin.Forms es fantástico... sin embargo no soluciona todos nuestros problemas cuando desarrollamos. 

Una de las principales "fallas" es la falta de una forma avanzada de mostrar mensajes al usuario. Sí, sí, con Xamarin.Forms tenemos los métodos `DisplayAlert`:  

```csharp  
await DisplayAlert("Traditional alert", "Traditional message", "It's not so cool");

var result = await DisplayAlert("Traditional alert", "Traditional message?", "OK", "Cancel");
TraditionalPromptLabel.Text = string.Format("Result {0}", result);
```  

Estos métodos solamente muestran un mensaje sencillo y permiten que el usuario responda con un valor booleano, y además requieren de tener una referencia a la página en la que queremos mostrar la alerta.  

Es por eso que cuando queremos obtener más información de quien está usando nuesta app podemos recurrir a un paquete de NuGet que nos permitirá hacer operaciones como:  

## Mostrar un mensaje "Toast"   
Con *UserDialogs* podemos mostrar mensajes en varias plataformas de manera que en Android lo hacemos con la popular `Toast`:  

```csharp  
UserDialogs.Instance.Toast("Toast message: <3", TimeSpan.FromMilliseconds(millis));
```  

<img src="/images/xamarin-forms__userdialogs__toast.png" title=""Login prompt"" />

## Mostrar un error
```csharp  
UserDialogs.Instance.ShowError("¡Error!", millis);
```  

## Mostrar un mensaje de éxito 
```csharp  
UserDialogs.Instance.ShowSuccess("¡Éxito!", millis);
```  

Pero no solo eso, también podemos recibir información a través de UserDialogs

## PromptAsync  
Solicitar información del usuario, ya sea que estés buscando un nombre, un teléfono, un password...

```csharp  
var promptConfig = new PromptConfig();
promptConfig.InputType = InputType.Name;
promptConfig.IsCancellable = true;
promptConfig.Message = "Write your name";
var result = await UserDialogs.Instance.PromptAsync(promptConfig);
if (result.Ok)
{
    PromptedTextLabel.Text = result.Text;
}
```  

<img src="/images/xamarin-forms__userdialogs__text.png" title=""Text prompt"" />

## PromptDateAsync  
Solicitar una fecha, presentando un control muy familiar dependiendo de la plataforma en que se ejecute:

```csharp  
var result = await UserDialogs.Instance.DatePromptAsync(
    "Select date",
    DateTime.Now);
if (result.Ok)
    SelectedDateLabel.Text = String.Format("{0:dd MMMM yyyy}", result.SelectedDate);
```  

<img src="/images/xamarin-forms__userdialogs__calendar.png" title=""Calendar prompt"" />

## PromptTimeAsync  
Al igual que con la fecha, este diálogo presenta un contol para seleccionar una hora  

```csharp  
var result = await UserDialogs.Instance.TimePromptAsync(
    "Select time",
    DateTime.Now.TimeOfDay);
if (result.Ok)
    SelectedTimeLabel.Text = String.Format("{0:hh-mm}", new DateTime(result.SelectedTime.Ticks));
```  

<img src="/images/xamarin-forms__userdialogs__time.png" title=""Time prompt"" />

## PromptLoginAsync  
Si quieres algo un poco más compuesto como una "pantalla" de *login*, también puedes usar esta pequeña librería:

```csharp  
var loginResult = await UserDialogs.Instance.LoginAsync("Login", "Please sign in");
if (loginResult.Ok)
{
    PromptedLoginLabel.Text = String.Format("{0}:{1}", loginResult.Value.UserName, loginResult.Value.Password);
}
```  

<img src="/images/xamarin-forms__userdialogs__login.png" title=""Login prompt"" />

## Inyección de dependencias  
Como mencioné anteriormente el mecanismo de diálogos que provee Forms por default requieren de tener una referencia a la página en la que estamos trabajando, sin embargo *UserDialogs* no, y eso lo hace ideal para usarlo en conjunto con una solución de <a href="http://thatcsharpguy.com/tv/inyeccion-dependencias/" target="_blank">inyección de dependencias</a>.

## Personalización  
En este post mostré únicamente las opciones más sencillas de esta librería, sin embargo también podemos personalizarla casi tanto como deseamos, es por eso que te propongo que <a href="https://github.com/ThatCSharpGuy/UserDialogs-Sample" target="_blank">descargues el código</a> de ejemplo y practiques con él.

# Instalación  
Como siempre, hay que buscar en el gestor de paquetes de NuGet: `Acr.UserDialogs`

O desde la consola: 

```  
PM> Install-Package Acr.UserDialogs
```  

Recuerda que si lo usas en Xamarin.Forms debes instalar Acr.UserDialogs en todos tus proyectos (núcleo y clientes) para que funcione correctamente.
