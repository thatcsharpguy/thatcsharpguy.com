---
layout: post
title: async y await (cancelando tareas)
date: 2017-02-28 19:00:01
author: Antonio Feregrino
summary: Aprende la forma &quot;correcta&quot; para proveer a tus Task de un mecanismo de cancelación, además aprende cómo reportar el progreso de una tarea al usuario mediante una interfaz definida por el Framework.
featured_image: featured.png
images_folder: /aprende-c-sharp/asyncawait/
github: https://github.com/ThatCSharpGuy/AsyncAwait
lang: es
tags: AprendeCSharp, XamarinForms, Xamarin
featured_tag: AprendeCSharp
---

Una vez que conocimos los tipos `Task` y las apalabras `async` y `await`, y cómo es que nos ayudan a ejecutar código de manera asíncrona, es momento de comenzar a extender este poder para sacarle más ventaja.  

En este post veremos cómo es que podemos ejecutar tareas asíncronas de manera paralela. Ojo que el paralelelismo conlleva más complicaciones, pero este es un buen lugar para comenzar.  

## Ejemplo  
Nuevamente para seguir trabajando con este post tal vez quieras descargar la aplicación <a href="https://github.com/ThatCSharpGuy/AsyncAwait" target="_blank">demo que puedes encontrar en GitHub</a>. Para demostrar cómo podemos cancelar las tareas vamos a hacer uso deuna `ProgressBar` y unos cuantos botones. En este caso vamos a crear una tarea infinita que moverá de 0 a 100% la barra de progreso, esta es la tarea que vamos a cancelar con las técnicas que veremos en este post:


```csharp  
public static async Task SlowTask(IProgress<double> progress, CancellationToken token)
{
    while (true)
    {
        for (int i = 0; i < 100; i++)
        {
            await Task.Delay(10);
            // ???
            progress.Report(i / 100d);
        }
        for (int i = 100; i > 0; i--)
        {
            await Task.Delay(10);
            // ???
            progress.Report(i / 100d);
        }
    }
}
```  

Un momento... aquí hay un par de cosas nuevas: La interfaz `IProgress<T>` y  la clase `CancellationToken`, comenzaremos por la primera.  

## IProgress<T>  
Probablemente quieras separar tu código en varias capas, dejando en librerías separadas la capa de la interfaz y la capa de lógica. Pero, ¿en este escenario cómo podrías actualizar la *UI* desde una `Task`?

Lo primero que se te podría ocurrir es usar <a href="..\delegados-en-c-sharp" target="_blank">delegados</a>, o alguna <a href="..\func-y-action-en-c-sharp" target="_blank"><code>Action</code></a> para "inyectar" código dentro de la tarea. Pero por otro lado podríamos hacer uso de la interfaz `IProgress<T>` del framework.   

Esta interfaz define un solo método: `Report` que como su nombre lo indica nos sirve para reportar el progreso de una tarea desde una tarea asíncrona. En este caso, en el siguiente código se muestra la implementación de `IReport<double>` del ejemplo:  

```csharp  
public partial class AdvancedPage : ContentPage, IProgress<double>
{
    public void Report(double value)
    {
        Device.BeginInvokeOnMainThread(() => 
        {
            ProgressBar.Progress = value;
            IntegerReport.Text = (value * 100).ToString();
        });
    }
```  

Entonces, para ejecutar la tarea tenemos que llamarla así:  

```csharp  
var task = UltraComplexLibrary.SlowTask(this);
```  

En donde `this` es una instancia de la clase `AdvancedPage`.  

## `CancellationToken`
Ahora que podemos reportar el progreso de la una tarea asíncrona es probable que en algun momento deseemos cancelar una tarea, ya sea porque el usuario ya no está interesado en el resultado o porque la tarea está tardando mucho tiempo en ejecutarse. Al igual que para reportar el progreso, existen otras formas para cancelar una tarea, sin embargo, la más recomendable es hacer uso de un `CancellationToken` al cual me referiré de ahora en adelante solamente como *token*.  

### `CancellationTokenSource`

Este tipo de *tokens* se deben obtener de una *fuente*, en este caso de la clase `CancellationTokenSource`. Tanto el *token* como la *fuente* están fuertemente relacionadas y no se entiende la existencia de una sin la otra.  

Crear una *fuente* de *tokens* es sencillo, basta con usar el operador `new`:

```csharp  
CancellationTokenSource CancellationTokenSource;
// ...
CancellationTokenSource = new CancellationTokenSource();
```  

Y para obtener un *token* es necesario llamar a la propiedad `Token`, entonces la llamada a nuestra tarea cancelable queda como sigue:  

```csharp  
var task = UltraComplexLibrary.SlowTask(this, CancellationTokenSource.Token);
```  

### Cancelando la tarea  
El primer paso para cancelar una tarea es llamar al método `Cancel` sobre la *fuente*. Ya sea que lo llamemos directamente nosotros, o especifiquemos que se cancele después de cierta cantidad de tiempo:


```csharp  
CancellationTokenSource.Cancel();  
CancellationTokenSource.CancelAfter(3000);
```  

Ahora que dentro de nuestra tarea tenemos un *token* tenemos dos maneras principales para cancelarla:  

 - La primera: Es menos agresiva, es consultar al token si es que se ha solicitado su cancelación mediante la propiedad `IsCancellationRequested`. La tarea se termina cuando se llega a un `return`.  

```csharp  
if (token.IsCancellationRequested)
    return;
```  

 - La segunda: que es muy agresiva ya que implica lanzar una excepción si se ha solicitado que el token sea cancelado mediante el método `ThrowIfCancellationRequested`. La tarea se termina cuando se produce una exepción no controlada.

```csharp  
token.ThrowIfCancellationRequested();
```  

Si optas por el caso de la excepción no olvides agregar el código pertinente para manejarla en tu código y que no haga que falle por completo tu aplicación.

Usar una excepción o retornar manualmente de una tarea dependerá enteramente de tu criterio y una manera es tan válida como la otra.  

## Para terminar  
Como puedes ver el cancelar una tarea requiere de un par de componentes extras así como de incluir dentro de la implementación de la tarea la posibilidad de cancelarla. Es por eso que desde un inicio debes valorar si código debe ofrecer la opción de ser cancelado mediante el uso de *tokens*.  

Si estás usando una API de alguien más que no dejó abierta la posibilidad de cancelar la ejecución del código puedes hacer uso de el método `Task.WhenAny` para "simular" la cancelación. Puedes ver más en esta respuesta de StackOverflow: <a href="http://stackoverflow.com/a/27240225" target="_blank">answer to How can I cancel Task.WhenAll? [VB]</a>.  


> Puedes ver mi otro post sobre <a href="..\async-await" target="_blank">cómo crear tareas asíncronas</a> o este otro sobre <a href="..\async-await-2" target="_blank">cómo ejecutar tareas paralelamente</a>.