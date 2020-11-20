layout: post
title: Inspect your http requests with HttpLogger
date: 2017-03-28 19:00:01
author: Antonio Feregrino
excerpt: Inspect the request and response of all the connection your app makes to any webservice. Do whatever you want with them, from saving them to a file to put them on screen!
featured_image: featured.jpg
images_folder: /nugets/httplogger/
github: https://github.com/messier16/HttpLogger
lang: en
tags: NuGetRecomendado, AprendeCSharp
featured_tag: NuGetRecomendado

You can't imagine the number of headaches that trying to find bugs in the code of my apps that connect to web services... it gets even worse when I'm using the raw `HttpClient` or `WebClient` but the clients developed specifically for each API.

Yes, you're right, there are applications like <a href="http://www.telerik.com/fiddler" target="_blank">Fiddler</a> or <a href="https://www.wireshark.org/" target="_blank">Wireshark</a> that help you review all the requests and responses of all HTTP connections that happen in a certain device, however, in some occasions it turns out to be complex to setup a test environment or to configure such application to listen to what you are really looking for. With that in mind I began to search online...  

Then I found this <a href="http://stackoverflow.com/a/18925296" target="_blank">StackOverflow answer</a>, where the replier talks about a way to get the request and response from any `HttpClient`s outgoing petition, I took that code and turned into a small NuGet package that I'll show you in the following lines.  

## HttpLoggingHandler  
The "magic" starts by creating an object from the `HttpLoggingHandler` class. This class provides two constructors:  

 - One only receives an instance of `HttpMessageHandler`  
 - The other receives, apart from an `HttpMessageHandler`, a couple of `Func<HttpRequestMessage, Task>` where you are to describe what should happen with the *request* and *response*  

If you decide to use the single parameter constructor, the default actions will be used. By default, both the request and response are printed to console, should you decide to do anything complex, you must use the second constructor to provide your own code.  

For example, I've created a Xamarin.Forms that makes a request to the <a href="https://pokeapi.co/" target="_blank">Pokéapi</a>  and both the request and response are shown on the screen device, to accomplish this task I created a couple of methods:  

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

These two methods take all the data and presents it on screen in a *friendly* manner. To make use of them it is necessary pass them through the constructor of our `HttpLoggingHandler`, and then pass the instance of our *logging handler* to the constructor of `HttpClient`:  

```csharp  
var loggingHandler = new HttpLoggingHandler(new HttpClientHandler(), 
                                            RequestAction, 
                                            ResponseAction);
var client = new HttpClient(loggingHandler);
```  

Then we can make all the requests we want in the same way we used to:  

```csharp  
await client.GetStringAsync("https://pokeapi.co/api/v2/pokemon/1/");
```  

The above code will show the following in your screen device:  

<img src="/images/nugets__httplogger__screen.jpg" title=""Screenshot"" />

## Ready for production  
It is probable that you want to use this component to log only when you're debugging, so you can combine it with Paul Betts' <a href="https://www.nuget.org/packages/modernhttpclient/" target="_blank">modernhttpclient</a> to increase the speed of request on your release builds. I usually add some `#if` directives to make use of the logger only when debugging:


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

To use the logger, all you need to do is install the <a href="https://www.nuget.org/packages/HttpLogger/" target="_blank">NuGet package</a>.

```  
PM> Install-Package HttpLogger
```  

But in reality the package is just a class, so you may want to copy the code from the class <a href="https://github.com/messier16/HttpLogger/blob/master/HttpLogger/HttpLoggingHandler.cs" target="_blank">HttpLoggingHandler</a> into your application codebase.