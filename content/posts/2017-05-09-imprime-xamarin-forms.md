---
layout: post
title: ¿Imprimir desde Xamarin.Forms?
date: 2017-05-09 19:00:01
author: Antonio Feregrino
summary: Imprime desde tu app hecha con Xamarin.Forms, ofrece a tus usuarios la posibilidad de generar reportes, tickets y facturas desde su teléfono celular y listas para imprimir.
featured_image: featured.png
images_folder: /xamarin-forms/print/
github: https://github.com/ThatCSharpGuy/formsprinting
lang: es
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

Sí, así como lo oyes. Imprimir desde una app... la verdad es que a mi me sonó un poco extraño cuando escuché este requerimiento puesto que nunca se me había ocurrido conectar una impresora directamente a un teléfono móvil. Y la verdad es que en la actualidad la necesidad de imprimir se ha visto superada por la posibilidad de enviar documentos electrónicos.

Pero bueno, imagina que por alguna razón tu app debe tener la capacidad de imprimir. Buscando por la red me encontré con el post <a href="https://codemilltech.com/xamarin-forms-e-z-print" target="_blank">Xamarin.Forms E-Z Print</a> en donde se habla justamente de esto. En este post trataré de explicar qué es lo que sucede.  

Si ya has generado reportes desde cualquier otra plataforma sabes que una de las dificultades de hacerlo radica en obtener el formato que al cliente le parezca el adecuado... sí, esa suele ser una preocupación real. Y para evitar esto, usando el método de Matthew Soucoup nos vamos a auxiliar del lenguaje HTML para poder especificar el formato para nuestro documento.  

## El modelo  

En esta aplicación demo (<a href="https://github.com/ThatCSharpGuy/formsprinting" target="_blank">que puedes obtener en GitHub</a>) te permite añadir productos y generar después un ticket de compra. Para ello cree dos clases `Orden` y `Producto`, a lo cual llamaremos el Modelo.

```csharp  
public class Orden
{
    public int IdOrden { get; set; }
    public string Cliente { get; set; }
    public List<Producto> Productos { get; set; }
    public double Total => Productos.Sum(p => p.Total);
}

public class Producto
{
    public string Nombre { get; set; }
    public double Total { get; set; }
}
```  


## Template  

Para comenzar, debemos añadir una <a href="https://developer.xamarin.com/guides/cross-platform/advanced/razor_html_templates/#Razor_Template_Basics" target="_blank">plantilla Razor</a> (únicamente he visto disponible esta opción en Xamarin y Visual Studio para Mac). 

<img src="/images/xamarin-forms__print__razor-template-creation.png" title=""Add Razor template"" />

Esto creará un archivo `.cshtml` el cual debemos tratar como cualquier otro archivo `html` con algunas pequeñas diferencias que a continuación veremos:

#### Especificando el modelo  

Usualmente las plantillas Razor tienen asociada una clase de la cual se toman los datos para ser mostrados. En la primera (después de la declaración de los `using`) línea, se debe especificar cuál es esta clase. Esto se realiza mediante la instrucción `@model`:  

```csharp  
@model Ticket
```  

#### Es un documento normal  

Al seguir siendo un documento `html` no olvides especificar los *tags* *html*, *head* y demás. Oh, y también nota la especificación de la etiquieta *link*, esto lo veremos más adelante:

```xml  
<!DOCTYPE html>  
<html lang="en">  
    <head>
		<link rel="stylesheet" href="ticket.css" />
    </head>
    <body>
        <!-- contenido -->
    </body>
</html>  
```  

#### El contenido  

Ahora sí, vamos a crear el contenido. En este punto hay algo que debes comprender: no esperes crear complejas animaciones y efectos con CSS y que funcionen en tus documentos, para el diseño de tu plantilla vete a lo sencillo. En este caso yo usé tablas para ordenar los elementos que deseaba mostrar.  

Para acceder a los valores del modelo vamos a hacer uso de la propiedad `Model` del template, para hacer referencia a esta propiedad debemos anteponer el símbolo `@` (nota también la etiqueta *img*):  

```xml  
		<table class="header">
			<tr>
				<td>
					<h1>Ticket: @Model.IdOrden</h1>
					<h3>Cliente: @Model.Cliente</h3>
				</td>
				<td><img src="logo.png" width="100px"></td>
			</tr>
		</table>
```  

Las plantillas Razor también permiten usar sentencias de control como `foreach` para darle forma al documento:  

```csharp  
		<table>
			<thead>
				<tr>
					<td>Producto</td>
					<td>Precio</td>
				</tr>
			</thead>
			@foreach(var prod in Model.Productos)
			{
				<tr>
					<td>@prod.Nombre</td>
					<td class="precio">@prod.Total</td>
				</tr>
			}
			<tfoot>
				<tr>
					<td class="total">Total</td>
					<td class="precio total">@Model.Total</td>
				</tr>
			</tfoot>
		</table>
```  

Hasta aquí llega la creación de la plantilla, ahora vamos a ver cómo se usa.

## Usando la plantilla  

Para hacer uso de la plantilla hay que crear in objeto de la clase asociada (en este caso es `TicketTemplate`), establecer su propiedad `Model` a una instancia del modelo que vamos a asociar con ella. Después, con el método `GenerateString` provocamos que se genere la plantilla y obtengamos una cadena de ella.  

```csharp  
var order = new Orden()
{
    // Propiedades
};

var template = new TicketTemplate();
template.Model = ticket;

var rendered = template.GenerateString();
```  

La cadena `rendered` contiene el html de la plantilla, pero que ya contiene la información del modelo asociado. El siguiente paso es ayudarnos de la clase `HtmlWebViewSource` y del `WebView`. 

La clase `HtmlWebViewSource` permite establecer la fuente de datos de un `WebView` a una cadena que contiene el código que debe mostrarse dentro de él, así que si quieres mostrar una previsualización de tu documento, mostrar el `WebView` es una buena idea.

```csharp  
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = rendered;

var browser = new WebView();
browser.Source = htmlSource;
```  

## Enviando la impresión  

Enviar la impresión al dispositivo conectado es una tarea que es distinta para cada plataforma, es por eso que vamos a hacer uso de <a href="https://developer.xamarin.com/guides/xamarin-forms/application-fundamentals/dependency-service/introduction/" target="_blank">servicio de dependencias</a> de Xamarin para compartir código específico a cada plataforma.

Comenzamos por crear la interfaz `IPrinter`, que contará con un único método que recibirá el `WebView` que contiene nuestra plantilla generada:  

```csharp  
public interface IPrintService
{
    void Print(WebView viewToPrint);
}
```  

### En iOS 

La implementación de iOS consiste en tomar el `WebView` que recibe y obtener el control nativo (UIWebView). De ahí, obtener una instancia de `PrintInfo`, y es aquí en donde puedes configurar la impresión: tipo de colores, impresión a dos lados, etcétera. Por último, debes obtener la instancia compartida de  `PrintController`, la cual se debe presentar al usuario.

```csharp  
public class IosPrinter : IPrinter
{
    public void Print(WebView viewToPrint)
    {
        var renderer = Platform.CreateRenderer(viewToPrint);
        var webView = renderer.NativeView as UIWebView;
        var printInfo = UIPrintInfo.PrintInfo;
        printInfo.OutputType = UIPrintInfoOutputType.General;
        printInfo.JobName = "Xamarin.Forms printing";
        printInfo.Orientation = UIPrintInfoOrientation.Portrait;
        printInfo.Duplex = UIPrintInfoDuplex.None;
        var printController = UIPrintInteractionController.SharedPrintController;
        printController.PrintInfo = printInfo;
        printController.ShowsPageRange = true;
        printController.PrintFormatter = webView.ViewPrintFormatter;
        printController.Present(true, (printInteractionController, completed, error) => { });
    }
}
```  

### En Android  

En Android también hay que obtener la vista nativa (se llama `WebView` también, pero usé un <a href="http://thatcsharpguy.com/post/creando-propios-alias/" target="_blank">alias</a> para renombrarlo a `DroidWebView`). Luego hay que obtener un `PrintDocumentAdapter`, puedes escribir el tuyo propio para establecer las propiedades de la impresión... pero por ahora usaremos el que nos da por default el `WebView` nativo.  Después hay que obtener el servicio de `PrintManager` y finalmente solicitar la impresión.

```csharp  
public class DroidPrinter
{
    public void Print(WebView viewToPrint)
    {
        var renderer = Platform.CreateRenderer(viewToPrint);
        var webView = renderer.ViewGroup.GetChildAt(0) as DroidWebView;
        if (webView != null)
        {
            var version = Build.VERSION.SdkInt;
            if (version >= BuildVersionCodes.Kitkat)
            {
                var documentAdapter = webView.CreatePrintDocumentAdapter();
                var printMgr = (PrintManager)Forms.Context.GetSystemService(Context.PrintService);
                printMgr.Print("Forms-EZ-Print", documentAdapter, null);
            }
        }
    }
}
```  

### En el proyecto de Forms  

Para ejecutarlo en el proyecto de Forms basta con obtener una referencia del servicio de dependencias y llamar al método `Print`:  

```csharp  
var printService = DependencyService.Get<IPrinter>();
printService.Print(browser);
```  

## Para terminar  

Si bien te va a costar un poco de trabajo lograr que la impresión salga como tu deseas, al final el resultado valdrá la pena y así harás que tu app tenga un *plus* frente a otras. Oh, y si estás buscando si esto mismo se puede hacer para Windows... pues si se puede, pero es un tanto más complicado.  

Vuelvo a hacer referencia al <a href="https://codemilltech.com/xamarin-forms-e-z-print" target="_blank">post original</a>, en donde se expone esta idea. Así mismo te invito a descargar el <a href="https://github.com/ThatCSharpGuy/formsprinting" target="_blank">código de la app de ejemplo</a>.