---
layout: post
title: async y await (tareas paralelas)
date: 2017-02-21 19:00:01
author: Antonio Feregrino
summary: Trabaja con tareas asíncronas y paralelas en tus aplicaciones móviles, de escritorio y hasta en el servidor.
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
Vamos a seguir trabajando con la aplicación <a href="https://github.com/ThatCSharpGuy/AsyncAwait" target="_blank">app demo que puedes encontrar en GitHub</a>. En este caso usaremos dos `ProgressBar`, una `Label` y unos cuantos botones. En código de la app tendremos dos métodos que animan las barras de progreso a distintas velocidades:

```csharp  
async Task EjecutaTareaAsincrona1()
{
    await Task.Factory.StartNew(() =>
    {
        for (int i = 0; i < 100; i++)
        {
            Task.Delay(40).Wait();
            Device.BeginInvokeOnMainThread(() =>
            {
                ProgressBar1.Progress = ((double)i + 1) / 100;
            });
        }
    });
}

async Task EjecutaTareaAsincrona2()
{
    await Task.Factory.StartNew(() =>
    {
        for (int i = 0; i < 100; i++)
        {
            Task.Delay(100).Wait();
            Device.BeginInvokeOnMainThread(() =>
            {
                ProgressBar2.Progress = ((double)i + 1) / 100;
            });
        }
    });
}
```  

## Secuencial  
Lo primero que se viene a la mente es llamarlos consecutivamente haciendo como en el siguiente ejemplo:

```csharp  
TaskStatusLabel.Text = "Tarea iniciada";
await EjecutaTareaAsincrona1();
await EjecutaTareaAsincrona2();
TaskStatusLabel.Text = "Tarea terminada";
```  

Sin embargo lo que esto provocará es que se ejecute uno primero seguido del otro, no ambos al mismo tiempo que es lo que deseamos:    

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/asyncawait/sequential.gif" title="Bloqueo de hilo" />

## WhenAll  
Para ejecutar múltiples `Task` al mismo tiempo tenemos una solución bastante simple y sencilla de implementar, basta con obtener referencias hacia las tareas que queremos ejecutar (recuerda, `Task` es un tipo de dato como cualquier otro)  y usarlos como argumento del método estático `WhenAll` de la clase `Task`. Este método recibe una colección de tareas <a href="#" target="_blank">usando params</a> y las engloba en una una tarea padre que se considera terminada hasta que todas sus tareas hijas hayan terminado:

```csharp  
TaskStatusLabel.Text = "Tarea iniciada";
var t1 = EjecutaTareaAsincrona1();
var t2 = EjecutaTareaAsincrona2();
await Task.WhenAll(t1, t2);
TaskStatusLabel.Text = "Tarea terminada";
```  

De este modo tendremos tareas que se ejecutan simultáneamente y al final regresan todas al punto donde el código debe continuar: 

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/asyncawait/whenall.gif" title="Bloqueo de hilo" />

## WhenAny  
De manera similar a `WhenAll`, el método `WhenAny` agrupa a las tareas, sin embargo la tarea padre se considera terminada cuando al menos una de sus tareas hijas ha terminado. Cuando esto sucede el bloque de código continua ejecutándose de manera normal y las tareas que no terminaron siguen ejecutándos, solo que no regresan al hilo que las lanzó.

```csharp  
TaskStatusLabel.Text = "Tarea iniciada";
var t1 = EjecutaTareaAsincrona1();
var t2 = EjecutaTareaAsincrona2();
await Task.WhenAny(t1, t2);
TaskStatusLabel.Text = "Tarea terminada";
```  

Como puedes ver en este ejemplo, la barra superior termina antes que la inferior, como resultado el código que cambia el texto de la etiqueta de abajo cambia sin importar que una tarea quede pendiente de concluir:

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp/asyncawait/whenany.gif" title="Bloqueo de hilo" />

## Bloqueando con tareas  
A pesar de estar diseñadas para ejecutarse de manera asíncrona, es posible forzar a que las `Task` se comporten como tareas síncronas. Esto no es recomendable, pero si posible a través del método de instancia `Wait` para una tarea y los métodos de clase `WaitAll` y `WaitAny`.  

## Bonus: `Task` en el servidor  
Usualmente la asincronía se nota más en aplicaciones cuyo procesamiento se realiza directamente en un dispositivo cliente, sin embargo también se benefician las aplicaciones de servidor, como las creadas con Web API o MVC.  

La cosa funciona así: En el servidor existe un conjunto de hilos que están a la espera de atender las peticiones que reciba el servidor. Cada que llega una petición, se despacha un hilo para que este la atienda, y no se libera hasta que la termina de atender. Si tu aplicación hace uso de `async` y `await`, el hilo principal se desocupa mientras espera la respuesta de la tarea que despachó, mientras espera, puede atender otras peticiones, acelerando así el tiempo de respuesta de tu aplicación web.  

 > Puedes ver mi otro post sobre <a href="..\async-await" target="_blank">cómo crear tareas asíncronas</a> o este otro sobre <a href="..\async-await-3" target="_blank">cómo cancelarlas</a>.