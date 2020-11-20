---
layout: post
title: MarkdownView control for Xamarin.Forms
date: 2016-02-01 21:00:00
author: Antonio Feregrino
excerpt: Taking advantage of the vast number of implementations of Md converters out in the wild, I decided to create a MarkdownView control, one that could display Md documents without much hassle from the developer's point of view.
lang: en
featured_image: featured.png
github: https://github.com/ThatCSharpGuy/MarkdownView
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

By this time you definetly must have heard about the Markdown markup language. If you think you have not, here is a hint for you: Is that language where you use `#` do denote headings, `*` for emphasis and `**` for strong emphasis... I'm sure you know what I'm talking about. 

The thing with Markdown is that it is becoming more and more popular among almost every person who writes stuff in a computer due its simplicity and ease of use. I mean, it is readable at naked eye and you don't need a fancy editor to work with it, in fact: this blog post is written with Markdown.

While the Markdown is by itself a language that does not depend on anything else to stand, it is often associated with HTML because is one of the format to which Md is converted to in order to be displayed to an end user.  
 
And that bring us where I wanted: Taking advantage of the vast number of implementations of Md converters out in the wild, I decided to create a MarkdownView control, one that could display Md documents without much hassle from the developer's point of view.  
  
## CommonMark  
To convert from Md to HTML this control uses the <a href="https://github.com/Knagis/CommonMark.NET" target="_blank">CommonMark.NET</a> converter, in which, as the name states, the CommonMark is used to make the conversion between the two languages. I decided to stick with CommonMark because it is widely (if not the most) used online and it is endorsed by some big tech companies.  

## Just a wrapper  
I didn't created the control from scratch, mostly because I thought it wasn't neccesary, since the output of the CommonMark.NET library is HTML, all I needed was a simple wrapper around the existing `WebView`. So all the things that apply to that control (like <a href="https://developer.xamarin.com/guides/xamarin-forms/user-interface/webview/#Layout" target="_blank" >the layout issues</a>) are generally applicable to `MarkdownView`.

### HtmlWebViewSource  
The `WebView` control has a `Source` property that we can use to set its content, such property can be set to an `HtmlWebViewSource` that at the same time has an string property that holds the actual `Html` to be rendered in the view. This control takes advantage of that by building an `HtmlWebViewSource` with the output of the library.  

### Content  
The `MarkdownView` exposes a bindable property named `Markdown` of type `string` which must be used to set the markdown document's source, and everytime it changes, new content is rendered. This is the code that is called when such property changes:  

```csharp  
private void SetWebViewSourceFromMarkdown()
{
    string swapCssFunction =
        @"
function _sw(e){ 
    var cssFile = '" + _baseUrl + "'+e+'.css'; " +
    @"document.getElementById('_ss').setAttribute('href',cssFile);
}";
    string head = @"
<head>
<meta name='viewport' content='width=device-width, initial-scale=1.0, user-scalable=no'>
<link id='_ss' rel='stylesheet' href='#' >
<script>" + swapCssFunction + @"</script>
</head>";

    var body = @"
<body>" +
CommonMarkConverter.Convert(Markdown) + 
"</body>";

    Source = new HtmlWebViewSource { Html = "<html>" + head + body + "</html>", BaseUrl = _baseUrl };

    SetStylesheet();
}
```  

Wow wow wow, that is a lot of hardcoded html... but it is necesary to create a valid document to host our converted Markdown. This valid document will be the html of our MarkdownView soruce, this source is reset everytime the `Markdown` is changed. 

As you can see there is also a JavaScript function named `_sw` inside the document definition. It is there to swap the stylesheets by simply calling it, more on that later.  

### Setting the BaseUrl  
In order to work with local files inside a `WebView` we must set the BaseUrl property of the html source, each platform has its own local url, and that is why it has to be done through a mechanism like the dependency service, like in <a rel="nofollow" target="_blank" href="https://developer.xamarin.com/guides/xamarin-forms/user-interface/webview/#Local_HTML_Content">this Xamarin how-to</a>. I'm sure this can be done in the control renderer, though I have not found a way to do it.  

### Styling  
A well formed html document is readable by itself, though it may be not very pretty, that is why this control also allows setting a local css file as the stylesheet for the document, the control exposes the property `Stylesheet` which is a string and everytime its contents change, a JavaScript function is triggered inside the html of the WebView by calling `Eval` method and passing the desired stylesheet name, in fact, here is the code:

```csharp  
void SetStylesheet()
{
    if (!String.IsNullOrEmpty(Stylesheet))
    {
        Eval("_sw(\"" + Stylesheet + "\")");
    }
}
```  
  
Be aware that this control does not provide stylesheets, however, <a href="https://github.com/jasonm23/markdown-css-themes" target="_blank">here are some</a> that you can download and use with your application. Remember to put them into each platform appropiate folder: `Assets` for Android and Windows, `Resources` for iOS.    

### Where to get it
It is available on NuGet, just install the following package:

```  
PM> Install-Package Xam.Plugins.Forms.MarkdownView
```  

An example of its usage can be seen in my [MrkViewer app](/MrkViewer), I recently switched to it to show the content of the markdown documents. 