layout: post
title: CharpHat para Android
date: 2015-09-10 17:00:00
author: Antonio Feregrino
categories: blog es
github: http://github.com/fferegrino/CharpHat/
excerpt: Con CharpHat toma una foto y muéstrale al mundo que te gusta C#, CharpHat te permite colocarte un birrete con mucho estilo.
lang: es
featured_image: featured.png
alias: /charphat-android/index.html
tags: Xamarin, XamarinForms
featured_tag: XamarinForms

<div class="pure-g">
	<div class="pure-u-24-24">
		<a href="https://play.google.com/store/apps/details?id=com.thatcsharpguy.charphat" target="_blank">
			<img alt="Get it on Google Play" src="/postimages/charphat-android/header.png" />
		</a>
	</div>
</div>  
<br />
Con CharpHat toma una foto y muéstrale al mundo que te gusta C#, CharpHat te permite colocarte un birrete con mucho estilo.  
  
<br />
Ha pasado ya un buen rato desde que tuve el deseo de hacer esta aplicación para móvil, y es que la idea me llegó desde que comencé en el mundo de Xamarin. Pero al tener muy poca experiencia siempre había retrasado la idea. En fin, en este post te voy a explicar cómo es que hice la app con Xamarin.Forms y ayuda de los `custom renderers`.  
  
### El proyecto  
Para comenzar, el proyecto es una aplicación de Xamarin.Forms utilizando un proyecto compartido que cree desde Visual Studio. Una vez creado, deja cuatro proyectos en la solución, esta vez me enfocaré solamente en el proyecto de Android ya que es el que ya está desarrollado por completo. Primero, les muestro el proyecto compartido:

<img src="/postimages/charphat-android/shared-capture.png" />

Para darle un poco de organización dividí el código en carpetas, están:
<ul>
		
	<li><code>Controls</code>, contiene las abstracciones de controles que se deben implementar específicamente para cada plataforma, como es el caso de <code>StickerableImage</code>, que veremos más adelante.</li>
			
	<li><code>Helpers</code>, contiene recursos que se usan en toda la aplicación, como colores o estilos.</li>
			
	<li><code>Pages</code>, contiene las páginas que se usan a lo largo de toda la app, incluyéndo <code>CameraPage</code> que debe ser implementada por nosotros para cada plataforma, que veremos más adelante.</li>
			
	<li><code>Services</code>, contiene las abstracciones de caracterísitcas que también deben ser implementadas para cada plataforma.</li>
			
	<li><code>ViewModels</code>, contiene las clases que definen la lógica de la aplicación.</li>

</ul>  
  
### CameraPage  
La funcionalidad de la aplicación depende completamente de la posibilidad de tomar fotografías para después poderlas modificar, aquí es donde la cosa se pone un poco seria, puesto que dentro de todas las bondades que nos ofrece Xamarin.Forms, no está la posibilidad de tomar fotografías con código 100% compartido. Por ello se tiene que escribir código para cada plataforma, en este caso usando Xamarin.Android, es decir, **sigue siendo código en C#**.  

La implementación de la cámara es completamente una implementación basada en la que hizo Pierce Boggan en su aplicación Moments. Para usar un `custom renderer`  básicamente lo que se requiere es:  
<br />
**Declarar la abastracción**, en este caso es dentro de la carpeta `Pages` del proyecto compartido, en este caso `BasePage` deriva de `ContentPage`, esto es importante ya que es una manera de indicar qué es lo que vamos a implementar en la plataforma.
```csharp  
public class CameraPage : BasePage
{
	public CameraPage() { }
}
```  
**Implementar individualmente**, para realizar esto se debe crear una clase dentro del proyecto para el cual queremos implementar el control dentro de la plataforma, a notar que CameraPage deriva de PageRenderer (e implementa ISurfaceTextureListener, pero ese ya es otro tema):
```csharp  
public class CameraPage : PageRenderer, TextureView.ISurfaceTextureListener
```  
Dentro de esta clase, debemos sobreescribir el método `OnElementChanged`, ya que es donde se debe modificar el el control de acuerdo a nuestras necesidades, para este caso, en ese método de carga un `layout` que contiene la definición de la página en XML para Android y se añaden los manejadores de eventos. Para terminar, debemos **indicar a Xamarin.Forms** que nuestra clase derivada de `PageRenderer` debe ser usada para renderizar la página de la cámara en el dispositivo, esto se hace mediante el atributo `ExportRenderer`:
```csharp  
[assembly: ExportRenderer(typeof(CharpHat.Pages.CameraPage), typeof(CharpHat.Droid.Pages.CameraPage))]
```  
La implementación de la cámara usa las APIs viejas de Android, y no es para nada distinto de lo que se haría para implementar una funcionalidad similar usando Java, pero en este caso estarías usando fabuloso C#.  
  
### StickerableImage  
Otra de las características que tuvo que ser implementada de manera individual para cada plataforma fue la capacidad de añadir y manipular "stickers" usando nuestros dedos. Para ello, se deben seguir los pasos anteriores, es decir:  
<br /> 
**Declarar la abstracción**
```csharp  
public class StickerableImage : View
```  
En este caso, añadí dos propiedades a esta clase que permitirán manipular el tamaño (`ScaleFactor`) y el ángulo (`RotationFactor`) en el código del proyecto compartido, además las declaré como propiedades "bindeables", para poderlas usar desde XAML o con los <code>ViewModels</code>.
```csharp  
BindableProperty ScaleFactorProperty;
BindableProperty RotationFactorProperty;
```  
**Implementar individualmente**, para este caso, no se usa un renderizador conocido (como podría ser PageRenderer o ButtonRenderer), sino que se utiliza ViewRenderer en el que debemos especificar: el tipo del control en Xamarin.Forms y el tipo de control en la plataforma nativa (en este caso es Xamarin.Android) 
```csharp  
public class StickerableImageRenderer : ViewRenderer<StickerableImage, StickerView>
```  
Con el código anterior, lo que estamos diciendo es algo como: *Cuando sea Android, reemplaza StickerableImage por StickerView*, donde <code>StickerView</code> es un control del tipo <code>Android.Views.View</code>. Una vez hecho esto, únicamente resta indicarle a Forms que queremos usar ese Renderer para ese tipo de control, para ello usamos fuera del `namespace`:
```csharp  
[assembly: ExportRenderer(typeof(CharpHat.Controls.StickerableImage), typeof(CharpHat.Droid.Controls.StickerableImageRenderer))]
```  
  
#### StickerView  
Es un `View` que lo que hace es estar al pendiente de cuando el usuario toca la pantalla y dibuja en ese punto el peculiar birrete de C#, también permite modificar el tamaño y el ángulo de este, para poder acomodarlo de acuerdo a lo que indique el usuario.
  
### IPictureManager
Almacenar imagenes en el dispositivo también es algo que requiere de código específico para cada plataforma, es por eso que cree esta interfaz que será implementada independientemente y hará uso del Servicio de Dependencias de Xamarin.Forms. La interfaz expone un único método `SavePictureToDisk`, que toma los bytes de la imagen, el nombre que se le asignará y la carpeta en que se debe guardar. De nuevo, seguimos los pasos de siempre:  

**Declarar la abstracción**
```csharp  
public interface IPictureManager
{
	string SavePictureToDisk (string filename, string folder, byte[] imageData);
	// ...
```  
**Implementar individualmente**
```csharp  
public class PictureManager : IPictureManager
{
	public string SavePictureToDisk(string filename, string folder, byte[] imageData)
	{
		var dir = Android.OS.Environment.GetExternalStoragePublicDirectory(Android.OS.Environment.DirectoryPictures);
		string picsFolder = System.String.Empty;
		// ...
```  
Para usar el servicio de dependencias de Xamarin.Forms, debemos utilizar `DependencyAttribute` fuera del nombre de espacio en nuestra clase, como en la siguientes líneas:
```csharp  
[assembly: Xamarin.Forms.Dependency(typeof(CharpHat.Droid.Services.PictureManager))]
namespace CharpHat.Droid.Services
```  
  
### IScreenshotService
Para el caso de `IScreenshotService` se realizan pasos similares que para `IPictureManager`. La implementación de `IScreenshotService` permite capturar la pantalla que está viendo el usuario, úitil para cuando el birrete está en su posición y se quiere guardar esa imagen.  

### Conclusiones  
Como podemos ver, hay ciertas veces en que no nos podemos librar de escribir código para cada plataforma, sin embargo la gran mayoría de nuestra aplicación permanece compartida entre las tres plataformas. Son las peculiaridades como el acceso a la cámara del dispositivo y la captura de pantalla las que requieren de código C# aparte.  

#### Código compartido
Al momento de la publicación de este post, más de la mitad del código es compartido por la aplicación de Android, sin embargo estoy buscando maneras de reducir el porcentaje de código específico para cada plataforma.  
<br />
Por último, no te olvides de <a href="https://play.google.com/store/apps/details?id=com.thatcsharpguy.charphat" target="_blank">descargar la aplicación</a> en Google Play o <a href="http://github.com/fferegrino/CharpHat/">descargar el código fuente</a> en GitHub. Y espera pronto la versión para iOS y Windows Phone.



 
