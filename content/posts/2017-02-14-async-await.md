---
layout: post
title: async y await
date: 2017-02-14 19:00:01
author: Antonio Feregrino
summary: En este post voy a hablar acerca de uno de los mejores tipos de azúcar sintáctica que existen en el lenguaje de programación C#. Y con ello me refiero a las palabras async y await.
featured_image: featured.png
images_folder: /aprende-c-sharp/asyncawait/
github: https://github.com/ThatCSharpGuy/AsyncAwait
lang: es
tags: AprendeCSharp, XamarinForms, Xamarin
featured_tag: AprendeCSharp
---

¿Cuántas veces te ha tocado usar una aplicación que de pronto deja de responder? o peor aún, ¿cuántas veces tus aplicaciones se han quedado congeladas? esto sucede muchas veces porque el hilo encargado de manejar la interfaz gráfica está muy ocupado haciendo alguna tarea que le impide responder a la interacción con el usuario... y este problema es casi siempre causado por el desarrollador.  

Es común que este tipo de encrucijadas se resuelvan mediante la creación de hilos explícitamente. Tu creas tarea y le pides a un hilo que la ejecute, mientras tanto, programas al hilo principal para que esté al tanto de cuando esta tarea se completara para continuar trabajando con el resultado de dicha tarea. Es más, seguramente tu llegaste a trabajar con los famosos `Thread`, pero si no lo hiciste, te recomiendo que busques un poco de información sobre el manejo de hilos en C#, pero antes, continúa leyendo.   

En este post voy a hablar acerca de uno de los mejores tipos de <a href="../../tv/azucar-sintactica">azúcar sintáctica</a> que existen en el lenguaje de programación C#. Y con ello me refiero a las palabras `async` y `await`. Pero antes voy a introducir el código de ejemplo que estaré usando para esta demostración.

## Bloqueo de la UI

Para este post estoy usando <a href="../../tv/xamarin-forms" target="_blank">Xamarin.Forms</a>, y es una app bastante sencilla (cuyo código fuente debes descargar en <a href="https://github.com/ThatCSharpGuy/AsyncAwait" target="_blank">este enlace</a>) que contiene unos cuantos botones y una barra de progreso. En el siguiente código hace que cada 100 milisegundos la barra de progreso se mueva, así hasta llegar a 100:

```csharp  
void EjecutaTarea()
{
    ProgressBar.Progress = 0;
    for (int i = 0; i < 100; i++)
    {
        Task.Delay(100).Wait(); // Retardo
        ProgressBar.Progress = ((double)i + 1) / 100;
    }
}
```  

Aunque... en realidad el usuario no lo ve, ya que cuando nosotros llamamos al método `EjecutaTarea` la interfaz es bloqueada mientras se realiza la tarea, algo como lo que se muestra en el siguiente diagrama:  

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp__asyncawait__normal.png" title="Bloqueo de hilo" />

## Task  
Para evitarnos el bloquear el hilo podemos usar clase `Task`. Esta clase es una abstracción de un hilo, esta representa una tarea que puede ejecutarse en un hilo separado de la aplicación principal. Esta clase tiene dos variantes: una no genérica (`Task`) y una genérica (`Task<T>`). La primera nos ayuda cuando la tarea que le estamos asignando no debe regresar algun valor, mientras que la segunda nos ayuda cuando sí esperamos un valor de retorno.  

#### Breve aclaración  
Es una restricción establecida que no puedes modificar la interfaz gráfica fuera de un hilo que no sea el que la haya creado, es por eso que tanto en Android como en iOS existen mecanismos que le indican a una tarea que se debe ejecutar en el hilo de la interfaz gráfica. En el caso de Xamarin.Forms estos comportamientos están englobados en el método `BeginInvokeOnMainThread` que, en un dibujo, hace algo como esto:

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp__asyncawait__begin-invoke.png" title=""Invocación en el hilo de UI"" />

### Creando asincronía con Task.Run  
Para *crear-ejecutar* una tarea usaremos el método estático `Run` de la clase `Task`. Este método recibe un tipo `Action` o un tipo `Func<T>` (dependiendo de si queremos devolver un valor o no). El *action* o *func* es el código que queremos asociar con esa tarea. Vamos a tomar nuestro código anterior y envolverlo dentro de una `Task`:

```csharp  
void EjecutaTareaAsincrona()
{
    ProgressBar.Progress = 0;
    Task.Run(() =>
    {
        for (int i = 0; i < 100; i++)
        {
            Task.Delay(100).Wait(); // Retardo
            Device.BeginInvokeOnMainThread(() =>
            {
                ProgressBar.Progress = ((double)i + 1) / 100;
            });
        }
    });
}
```  

Hasta este punto la tarea se ejecutará en un hilo distinto al que en el que fue lanzada. Y mientras que esto ya no bloquea la interfaz, puede que no sea exactamente lo que estamos buscando, y es que cuando llamamos a `EjecutaTareaAsincrona` se dispara un nuevo hilo y la ejecución del código subsecuente continua, sin importarle que la tarea que acaba de lanzar termine o falle (una técnica conocida como *fire and forget*). Algo como lo que se ve en el siguiente diagrama:  

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp__asyncawait__simple-task.png" title="Fire and forget" />

## async y await  
Para solucionar ese *problema* es cuando aparecen las palabras mágicas: `async` y `await`. Las dos deben ser usadas en conjunto, aunque en realidad `await` es quien depende de `async`. Modifiquemos un poco el código anterior para hacer uso de este par de palabras:  

```csharp  
async Task EjecutaTareaAsync()
{
    ProgressBar.Progress = 0;
    await Task.Run(() =>
    {
        for (int i = 0; i < 100; i++)
        {
            Task.Delay(100).Wait(); // Retardo
            Device.BeginInvokeOnMainThread(() =>
            {
                ProgressBar.Progress = ((double)i + 1) / 100;
            });
        }
    });
}
```  

Primero, ¿notaste la palabra `async` en la firma del método? esta palabra reservada tiene una sola función: habilitar la palabra `await` dentro del cuerpo del método.  

Segundo: Cambió el tipo de retorno del método de `void` a `Task`, esto es una práctica estándar, ya que `Task` nos ayudará en el manejo de las excepciones que puedan llegar a ocurrir mientras se ejecuta el código. Recuerda: no es buena idea usar `async` en combinación con `void` salvo que se trate de un manejador de eventos.

Tercero: Pusimos la palabra `await` justo antes de ejecutar la tarea. Es aquí donde por fin *"controlamos"* los hilos. **Lo que hace esta palabra reservada es indicarle al programa que queremos que la tarea se ejecute en otro hilo, pero que cuando termine regrese y termine de ejecutar lo que le quedó pendiente en el hilo del que la lanzó**. Algo como lo que se muestra en este diagrama:

<img src="https://thatcsharpguy.github.io/postimages/aprende-c-sharp__asyncawait__async.png" title=""Async, sin bloqueo"" />

Se dice que *async* y *await* son contagiosos, ya que una vez que lo usas prácticamente todo tu código relacionado con un método asíncrono también se vuelve asíncrono. Así que para beneficiarnos realmente de la asincronía también debemos llamar a el método asíncrono de la siguiente manera `await EjecutaTareaAsync`, sí, con `await`. 

Hay todavía un montón de cosas por hablar sobre *async* y *await*, pero para no hacer tan largo este post, pronto subiré otro explicando algunas operaciones más *avanzadas*. Pero puedes irlas descubriendo en cuanto bajes <a href="https://github.com/ThatCSharpGuy/AsyncAwait" target="_blank">app de ejemplo</a>.  

> Puedes ver mi otro post sobre <a href="..\async-await-2" target="_blank">cómo ejecutar tareas paralelamente</a> o este otro sobre <a href="..\async-await-3" target="_blank">cómo cancelarlas</a>.