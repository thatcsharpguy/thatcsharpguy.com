---
layout: post
title: Inspeciona tus peticiones http con HttpLogger
date: 2017-03-28 19:00:01
author: Antonio Feregrino
summary: Inspecciona el request y response de las conexiones que tu app hace a servicios web. Haz con ellas lo que quieras, desde guardarlas en un archivo hasta ponerlas en pantalla.
featured_image: featured.jpg
images_folder: /nugets/httplogger/
github: https://github.com/messier16/HttpLogger
lang: es
tags: NuGetRecomendado, AprendeCSharp
featured_tag: NuGetRecomendado
---

Ni te imaginas la de dolores de cabeza que me ha dado el tratar de encontrar errores en el código de mis apps que se conectan con servicios web... y más aún cuando no uso directamente el `HttpClient` o `WebClient`, sino los clientes desarrollados para cada *API* en específico.

Es cierto, existen programas como <a href="http://www.telerik.com/fiddler" target="_blank">Fiddler</a> o <a href="https://www.wireshark.org/" target="_blank">Wireshark</a> que te ayudan a revisar las peticiones y respuestas de todas las conexiones HTTP que ocurren en determinado dispositivo, sin embargo en algunas veces resulta ser complicado preparar un entorno de prueba o configurar los programas para que estén al tanto de lo que realmente quieres, con eso en mente me puse a buscar en internet...

Hasta que me encontré con <a href="http://stackoverflow.com/a/18925296" target="_blank">una respuesta en StackOverflow</a>, en donde el que responde habla de una forma de obtener el *request* y *response* de las peticiones salientes de un `HttpClient`, entonces tomé ese código y lo convertí en un pequeño paquete de NuGet que a continuación te presento.

## HttpLoggingHandler  
La "magia" comienza con crear un objeto de la clase `HttpLoggingHandler`. La clase tiene dos constructores:  

 - Uno recibe únicamente una instancia de `HttpMessageHandler`  
 - Otro recibe, además de un `HttpMessageHandler`, un par de `Func<HttpRequestMessage, Task>` en donde debes las acciones que deseas que se realicen con el *request* y el *response*  

Si decides usar el constructor que recibe un solo parámetro, se usará el comportamiento por default de escribir a consola el *request* y el *response* de las conexiones hechas, por otro lado, si deseas hacer algo más complejo, debes usar el segundo. 

Por ejemplo, he creado una app con Xamarin.Forms que se conecta a la <a href="https://pokeapi.co/" target="_blank">Pokéapi</a> y tanto la petición como la respuesta se muestran en la pantalla del dispositivo, para lograr dicha tarea tuve que crear un par de métodos:  

```csharp  
async Task ResponseAction(HttpResponseMessage httpResponseMessage)
{
    string content = null;
    if (httpResponseMessage.Content != null)
    {
        content = await httpResponseMessage.Content.ReadAsStringAsync();
        content = content.Substring(0, Math.Min(100, content.Length)) + "...";
    }
    Device.BeginInvokeOnMainThread(() =>
    {
        var fs = new FormattedString();
        fs.Spans.Add(new Span { Text = "Status: " });
        fs.Spans.Add(new Span { Text = httpResponseMessage.StatusCode.ToString(), FontAttributes = FontAttributes.Bold });
        fs.Spans.Add(NewLine());
        if (httpResponseMessage.Headers.Any())
        {
            fs.Spans.Add(new Span { Text = "Headers:" });
            fs.Spans.Add(NewLine());
            foreach (var header in httpResponseMessage.Headers)
            {
                fs.Spans.Add(new Span { Text = "\t•" + header.Key + ": " });
                fs.Spans.Add(new Span { Text = String.Join(",", header.Value), FontAttributes = FontAttributes.Bold });
                fs.Spans.Add(NewLine());
            }
        }
        if (content != null)
        {
            fs.Spans.Add(new Span { Text = "Content: " });
            fs.Spans.Add(new Span { Text = content, FontAttributes = FontAttributes.Bold });
        }
        LabelResponse.FormattedText = fs;
    });
}

async Task RequestAction(HttpRequestMessage httpRequestMessage)
{
    string content = null;
    if (httpRequestMessage.Content != null)
    {
        content = await httpRequestMessage.Content.ReadAsStringAsync();
    }
    Device.BeginInvokeOnMainThread(() =>
    {
        var fs = new FormattedString();
        fs.Spans.Add(new Span { Text = "URL: " });
        fs.Spans.Add(new Span { Text = httpRequestMessage.RequestUri.ToString(), FontAttributes = FontAttributes.Bold });
        fs.Spans.Add(NewLine());
        fs.Spans.Add(new Span { Text = "Method: " });
        fs.Spans.Add(new Span { Text = httpRequestMessage.Method.ToString(), FontAttributes = FontAttributes.Bold });
        fs.Spans.Add(NewLine());
        if (httpRequestMessage.Headers.Any())
        {
            fs.Spans.Add(new Span { Text = "Headers:" });
            fs.Spans.Add(NewLine());
            foreach (var header in httpRequestMessage.Headers)
            {
                fs.Spans.Add(new Span { Text = "\t•" + header.Key + ": " });
                fs.Spans.Add(new Span { Text = String.Join(",", header.Value), FontAttributes = FontAttributes.Bold });
            }
        }
        if (content != null)
        {
            fs.Spans.Add(new Span { Text = "Content: " });
            fs.Spans.Add(new Span { Text = content, FontAttributes = FontAttributes.Bold });
        }
        LabelRequest.FormattedText = fs;
    });
}
```  

Que lo que hacen es tomar los datos y presentarlos en pantalla de una manera *amigable*. Para poner en uso estos métodos, es necesario pasárselos al constructor de la `HttpLoggingHandler`, y a su vez, pasarle la instancia del *logging handler* al `HttpClient`:  

```csharp  
var loggingHandler = new HttpLoggingHandler(new HttpClientHandler(), 
                                            RequestAction, 
                                            ResponseAction);
var client = new HttpClient(loggingHandler);
```  

Luego, podemos seguir realizando las peticiones como siempre las hemos hecho:  

```csharp  
await client.GetStringAsync("https://pokeapi.co/api/v2/pokemon/1/");
```  

Lo cual, mostrará el siguiente resultado en pantalla:  

<img src="/images/nugets__httplogger__screen.jpg" title=""Screenshot"" />

## Uso en producción  
Es probable que quieras hacer uso de este componente para hacer logging cuando estás debuggeando, así que puedes combinarlo con <a href="https://www.nuget.org/packages/modernhttpclient/" target="_blank">modernhttpclient</a> de Paul Betts para cuando compilas tus apps para ser publicadas en producción, usualmente yo agrego unas sentencias `#if` para conseguirlo:  


```csharp  
HttpClient client;
#if DEBUG
var loggingHandler = new HttpLoggingHandler(new HttpClientHandler(), 
                                            RequestAction, 
                                            ResponseAction);
client = new HttpClient(loggingHandler);
#else
client = new HttpClient(new NativeMessageHandler());
#endif
```  

Para instalarlo basta con instalar el <a href="https://www.nuget.org/packages/HttpLogger/" target="_blank">paquete de NuGet</a> HttpLoggingHandler.

```  
PM> Install-Package HttpLogger
```  

Aunque en realidad no es más que una sola clase, por lo que daría lo mismo que copiaras el código de la clase <a href="https://github.com/messier16/HttpLogger/blob/master/HttpLogger/HttpLoggingHandler.cs" target="_blank">HttpLoggingHandler</a> dentro de tu app.