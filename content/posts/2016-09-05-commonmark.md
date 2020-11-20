layout: post
title: CommonMark.NET
date: 2016-09-05 19:00:01
author: Antonio Feregrino
excerpt: Convertir de un documento escrito con el lenguaje Markdown a uno escrito en HTML con este paquete de NuGet es muy intuitivo, además de que su API permite una personalización de la salida bastante intuitiva.
featured_image: featured.png
images_folder: /commonmark/
github: https://github.com/ThatCSharpGuy/CommonMark.NET-Sample
lang: es
tags: NuGetRecomendado
featured_tag: NuGetRecomendado

De nuevo un <a href="#">#NuGetRecomendado</a>, esta vez hablaré de uno que tiene un caso muy específico de uso.

Seguramente habrás escuchado sobre Markdown, que es un lenguaje de marcado entendible fácilmente para los humanos (a diferencia de XML). Este lenguaje es principalmente usado para la creación de documentos HTML a partir de otros con el formato Markdown.

El formato fue creado por <a href="https://web.archive.org/web/20040402080322/http://www.aaronsw.com/weblog/001189" target="_blank">John Gruber y Aaron Swartz</a> con la idea de tener una forma sencilla de escribir un email, un post de blog (como este que estás leyendo), una respuesta en un foro o sitio web, usando una <a href="https://daringfireball.net/projects/markdown/syntax" target="_blank">sintaxis simple</a> y no tan invasiva con el documento. Una vez escrito el documento es necesario hacerlo pasar por un proceso de conversión a HTML.

Este NuGet realiza precisamente eso: la conversión de Markdown a HTML.

## Código  
Para el siguiente ejemplo, vamos a trabajar con el archivo llamado `input.md` cuyo contenido es:  
  
<pre>
Hello world
=  

Visita el post del [blog](http://thatcsharpguy.com/post/commonmark) para **saber más** sobre *CommonMark.NET*  
</pre>    

### Convert (Strings)
Si lo único que quieres es convertir En realidad la api de CommonMark es una de las más sencillas, el siguiente bloque de código convierte una cadena de texto (Markdown) en otra cadena de texto (HTML):

```csharp  
var file = File.ReadAllText("input.md");
var exp = CommonMark.CommonMarkConverter.Convert(file);
Console.WriteLine(exp);
```  

El resultado:  

<pre>
&lt;h1&gt;Hello world&lt;/h1&gt;
&lt;p&gt;Visita el post del &lt;a href=&quot;http://thatcsharpguy.com/post/commonmark&quot;&gt;blog&lt;/a&gt; para &lt;strong&gt;saber m&#225;s&lt;/strong&gt; sobre &lt;em&gt;CommonMark.NET&lt;/em&gt;&lt;/p&gt;
</pre>

### Convert (Streams)
Habrá ocasiones en las que necesitemos convertir archivos completos y hacerlo usando *streams* de datos, para esto, el método `Convert` tiene otra sobrecarga que permite realizar esta tarea. Previamente necesitamos abrir los flujos de datos (yo recomiendo hacerlo con <a href="../usos-using-2">el bloque using</a>):

```csharp  
using (var reader = new StreamReader("input.md"))
using (var writer = new StreamWriter("output.html"))
{
    CommonMark.CommonMarkConverter.Convert(reader, writer);
}
```  

Tras lo cual, si abrimos el archivo `output.html` con un navegador, veremos algo como lo siguiente:  

<img src="/images/commonmark__raw_output.png" title=""Salida"" />

Sin embargo, de esta forma lo único que estamos haciendo es escribiendo algunas etiquetas HTML en el documento de salida, falta escribir los tags `html`, `head`, `body`... entre otros para que el HTML sea válido.  

### Convert (conversión personalizada)
Para nuestra suerte, el desarrollador de CommonMark, nos permite configurar la forma en la que se *renderea*
 el resultado de la conversión. Esto, a través de la clase `CommonMarkSettings` y un derivado de `HtmlFormatter`:

```csharp  
class CustomHtmlFormatter : CommonMark.Formatters.HtmlFormatter
{
    // ... 
```  

#### WriteInline  
El método `WriteInline`, que permite modificar la manera en que se *renderea* un elemento en HTML. en el ejemplo siguiente se modifica la forma en la que se escriben las etiquetas `a` para que los enlaces se abran en una nueva pestaña:

```csharp  
    protected override void WriteInline(Inline inline, bool isOpening, bool isClosing, out bool ignoreChildNodes)
    {
        if (inline.Tag == InlineTag.Link) // Es enlace
        {
            ignoreChildNodes = false; // Queremos seguir procesando los nodos hijo

            //  Revisamos si es la etiqueta de apertura
            if (isOpening)
            {
                this.Write("<a target=\"_blank\" href=\"");
                this.WriteEncodedUrl(inline.TargetUrl);
                this.Write("\">");
            }

            //  Revisamos si es la etiqueta de cierre
            if (isClosing)
            {
                this.Write("</a>");
            }
        }
        else
        {
            // Usamos la implementación por default para otro los otros nodos
            base.WriteInline(inline, isOpening, isClosing, out ignoreChildNodes);
        }
    }
```  

#### WriteBlock

Pero esta es solo una opción de configuración, ya que por otro lado podemos sobrescribir el método `WriteBlock` para escribir bloques completos. En este caso, usaremos el método para escribir un documento HTML válido:  

```csharp  
    protected override void WriteBlock(Block block, bool isOpening, bool isClosing, out bool ignoreChildNodes)
    {
        // Si es un tag de apertura y es Document
        if (isOpening && block.Tag == BlockTag.Document)
        {
            this.Write("<html>");
            this.Write("<head>\n<meta charset=\"UTF-8\">\n<link href=\"https://github.com/jasonm23/markdown-css-themes/raw/gh-pages/markdown6.css\" rel=\"stylesheet\" ></head>");
            this.Write("<body>");
        }
        // Si es un tag de cierre y es Document
        else if (isClosing && block.Tag == BlockTag.Document)
        {
            this.Write("</body></html>");
        }

        // LLamamos a la implementación por default para procesar los otros nodos
        base.WriteBlock(block, isOpening, isClosing, out ignoreChildNodes);
    }
```  

Tras lo cual, podrías tener algo como esto:

<img src="/images/commonmark__raw_output_2.png" title=""Salida"" />

Gran diferencia, ¿no?

## Casos de uso  
Esta librería tiene una tarea en específico, y tal vez te resulte complicado encontrarle un uso, pero si como yo, estás <a href="#" target="_blank">creando un visor de Markdown</a> o, estás creando tu <a href="https://davidwalsh.name/introduction-static-site-generators" target="_blank">generador de sitios estáticos</a> (el blog usa jekyll, pero he pensado en crear un generador con C#).

## Conclusión  
Como pudiste observar, la API de CommonMark.NET es bastante sencilla de aprender y usar, en donde la cosa se puede tornar un poco complicada es cuando queremos personalizar el resultado final mediante la clase `CommonMarkSettings`.
 
# Instalación  
En el gestor de paquetes de NuGet: `CommonMark.NET`

O desde la consola: 

```  
PM> Install-Package CommonMark.NET
```  

Y al igual que con todos los NuGets, no olvides revisar el código fuente de *CommonMark.NET* <a href="https://github.com/Knagis/CommonMark.NET" target="_blank" >en GitHub</a>, en donde encontrarás gran parte de la <a href="https://github.com/Knagis/CommonMark.NET/blob/master/CommonMark/Formatters/HtmlFormatter.cs" target="_blank">documentación en el código</a>, lo cual es absolutamente fantástico.  
