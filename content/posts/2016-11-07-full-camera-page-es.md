layout: post
title: Cámara a página completa en Xamarin.Forms
date: 2016-11-08 08:00:00
author: Antonio Feregrino
excerpt: Aprende cómo tomar fotos desde tu aplicación de Xamarin.Forms usando un custom renderer para acceder a la cámar aisn dejar tu aplicación. Con este control puedes personalizar la interfaz de usuario para que coincida con tu app.
featured_image: featured.jpg
images_folder: /xamarin-forms/camerapage/
github: https://github.com/ThatCSharpGuy/Forms-FullCameraPage
lang: es
tags: XamarinForms, Xamarin
featured_tag: XamarinForms

Hace ya un tiempo desde que programé la aplicación<a href="..\Aharphat-Android" target="_blank">CharpHat</a>, que es una aplicación que permite tomar una foto de cualquier cosa y ponerle un birrete de C# en ella. La aplicación no era perfecta, pero me ayudó a practicar el uso de los *custom page renderers*.  

Ahora, decidí retomar el proyecto, pero en esta ocasión con la meta de aislar el código necesario para crear la interfaz y funconalidad de la cámara, de tal modo que cualquiera que quisiera implementar una cámara en su aplicación lo pudiera reutilizar en sus proyectos. Asegúrate de <strong><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage" target="_blank">descargar el código fuente</a></strong> para este post.

## Abstracciones de Forms
<small><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage/blob/master/CameraPage.cs" target="_blank">Aquí está el código fuente para esta sección.</a></small>  

Comencemos creando la página de Forms que nos servirá como medio de interacción con el código de clada plataforma:

```csharp  
public class CameraPage : ContentPage
{
    public delegate void PhotoResultEventHandler(PhotoResultEventArgs result);
    public event PhotoResultEventHandler OnPhotoResult;
```  

Nada fuera de lo normal, crea una clase que derive de `ContentPage`. Añadí un *event handler* ya que que queremos obtener la foto tomada por el usuario. Ahora, agreguémos algunos métodos para llamar cada vez que el usuario realice una acción en la página (en este caso, el usuario podrá tomar una foto o cancelar la acción):

```csharp  
public void SetPhotoResult(byte[] image, int width = -1, int height = -1)
{
    OnPhotoResult?.Invoke(new PhotoResultEventArgs(image, width, height));
}

public void Cancel()
{
    OnPhotoResult?.Invoke(new PhotoResultEventArgs());
} 
```  

Como referencia, mira las propiedades dentro de la clase `PhotoResultEventArgs`:

```csharp  
public bool Success { get; private set; }
public int Width { get; private set; }
public int Height { get; private set; }
public byte[] Image { get; private set; }
```  

Ahora es momento de seguir al código específico de cada plataforma. 

## In Xamarin.iOS    
<small><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage/blob/master/iOS/CameraPageRenderer.cs" target="_blank">Aquí está el código fuente para esta sección.</a></small>  

Para ser sincero, la implementación en iOS es la más sencilla por mucho. Comenzamos por crear una clase que derive de `PageRenderer` y le añadimos el atributo `ExportRenderer`:

```csharp  
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace FullCameraPage.iOS
{
	public class CameraPageRenderer : PageRenderer
```  

Ahora, y esto es muy importante, tienes que sobreescribir el método `ViewDidLoad` dado que es llamado tan pronto nuestra página es cargada por el sistema operativo. Para organizar un poco mejor el código, partamos el código en varios métodos:

```csharp  
public override async void ViewDidLoad()
{
    base.ViewDidLoad();
    SetupUserInterface();
    SetupEventHandlers();
    AuthorizeCameraUse();
    SetupLiveCameraStream();
}
```  

### SetupUserInterface 
Tal y como el nombre lo dice, aquí es donde tenemos que construir la interfaz. Como podrás haber adivinado, todo se hace con código, pero no te preocupes, es muy sencillo en tanto tu interfaz no sea muy complicada, pero puedes hacer lo que tu quieras aquí.  

Para este ejemplo la interfaz consistrá de un par de botones y una superficie donde la vista en vivo de la cámara se mostrará, así que declara las siguientes campos a nivel de clase:

```csharp  
VectorButton takePhotoButton;
VectorButton cancelPhotoButton;
UIView liveCameraStream;
```  

Ahora para poner los controles en su lugar necesitas pensar como si estuvieras trabajando con un *relative layout*, lo que significa que hay que poner la posición del control en la pantalla. Por ejemplo, observa cómo se ubica la vista de la cámara:

```csharp  
private void SetupUserInterface()
{
    // Code ommited ...
    liveCameraStream = new UIView()
    {
        Frame = new CGRect(0f, 0f, View.Bounds.Width, View.Bounds.Height)
    };
    // Code ommited ...
    View.Add(liveCameraStream);
    // Code ommited ...
}
```  

### SetupEventHandlers  
Ahora que ya hemos terminado la interfaz, podemos *conectar* los manejadores de eventos a cada control, por suerte en nuestro ejemplo únicamente tenemos dos botones en la pantalla: para tomar fotos y para cancelar la captura.

```csharp  
cancelPhotoButton.TouchUpInside += (s, e) =>
{
    (Element as CameraPage).Cancel();
};

takePhotoButton.TouchUpInside += async (s, e) =>
{
    var data = await CapturePhoto();
    UIImage imageInfo = new UIImage(data);

    (Element as CameraPage).SetPhotoResult(data.ToArray(),
                                           (int)imageInfo.Size.Width,
                                           (int)imageInfo.Size.Height);
};
```  

La propiedad `Element` contiene una referencia a la página asociada con el *renderer* y es nuestro medio de interacción con el proyecto de Forms. Si te preguntas sobre el método `CapturePhoto`, lo veremos más adelante.  

## AuthorizeCameraUse  
Ahora es momento de solicitar permiso al usuario para usar su cámara. 

```csharp  
var authorizationStatus = AVCaptureDevice.GetAuthorizationStatus(AVMediaType.Video);
if (authorizationStatus != AVAuthorizationStatus.Authorized)
{
    await AVCaptureDevice.RequestAccessForMediaTypeAsync(AVMediaType.Video);
}
```  

Pero antes de ejecutar el código anterior tienes que agregar la entrada `Privacy - Camera Usage Description` al archivo Info.plist en tu proyecto.  

### SetupLiveCameraStream  
Ahora la parte "complicada".
  
Comienza delcarando a nivel de clase una `AVCaptureSession`, `AVCaptureDeviceInput` y `AVCaptureStillImageOutput`,  ya que estos nos ayudaran a acceder a la cámara, mostrarla en vivo, y capturar la imagen.  

Entonces dentro de `SetupLiveCameraStream`, inicializa una sesión de captura, crea una capa de previsualización del mismo tamaño que nuestra `liveCameraStream`, y añádela como una subcapa de esta:

```csharp  
    captureSession = new AVCaptureSession();
    var videoPreviewLayer = new AVCaptureVideoPreviewLayer(captureSession)
    {
        Frame = liveCameraStream.Bounds
    };
    liveCameraStream.Layer.AddSublayer(videoPreviewLayer);
```  

Después, "crea" un dispositivo de captura (que puedes configurar de acuerdo a tus necesidades). Y entonces de éste dispositivo crea una entrada para la sesión de captura:

```csharp  
    var captureDevice = AVCaptureDevice.DefaultDeviceWithMediaType(AVMediaType.Video);
    ConfigureCameraForDevice(captureDevice);
    captureDeviceInput = AVCaptureDeviceInput.FromDevice(captureDevice);
```  

Ya tenemos una entrada (la cámara del dispositivo), ahora necesitamos una salida, que será una fotografía en formato jpeg:

```csharp  
    var dictionary = new NSMutableDictionary();
    dictionary[AVVideo.CodecKey] = new NSNumber((int)AVVideoCodec.JPEG);
    stillImageOutput = new AVCaptureStillImageOutput()
    {
        OutputSettings = new NSDictionary()
    };
```  

Terminamos estableciendo la entrada y salida de la sesión de captura y la iniciamos:

```csharp  
    captureSession.AddOutput(stillImageOutput);
    captureSession.AddInput(captureDeviceInput);
    captureSession.StartRunning();
```  

### CapturePhoto
Finalmente la cereza del pastel. El código para capturar la foto. En sí, el código es bastante simple: Toma la salida y obten una imagen fija de ella, ya que nosotros solo necesitamos los bytes (`NSData`) que contenga la foto tomada: 

```csharp  
public async Task<NSData> CapturePhoto()
{
    var videoConnection = stillImageOutput.ConnectionFromMediaType(AVMediaType.Video);
    var sampleBuffer = await stillImageOutput.CaptureStillImageTaskAsync(videoConnection);
    var jpegImageAsNsData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
    return jpegImageAsNsData;
}
```  

## In Xamarin.Android    
<small><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage/blob/master/Droid/CameraPageRenderer.cs" target="_blank">Aquí está el código fuente para esta sección.</a></small>      

Esta implementación no es tan limpia como en iOS. Principalmente porque Android hace más énfasis en el uso de *listeners* que en manejadores de evento. Como sea, ese no es un problema para nosotros.

Como en iOS, comienza creando una clase que derive de `PageRenderer` y también haz que implmente la interfaz `TextureView.ISurfaceTextureListener`. No olvides el atributo `ExportRenderer`:  

```csharp  
[assembly: Xamarin.Forms.ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace FullCameraPage.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
```  

Después, sobreescribe el método `OnElementChanged` (si ya has trabajado con *custom renderers* antes, este método te puede parecer familiar), ya que este será llamado cada vez que una `CameraPage` es mostrada en la pantalla:

```csharp  
protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
{
    base.OnElementChanged(e);
    SetupUserInterface();
    SetupEventHandlers();
```  

### SetupUserInterface  
En este método se debe crear la interfaz de la cámara, puedes hacerlo mediante un archivo *axml* e "inflarlo" con los mecanismos de Android... o, como en este ejemplo, crearlo en código.  

Para este ejemplo vamos a necesitar un `RelativeLayout` como contenedor, un `TextureView` para mostrar la cámara y un botón para tomar la foto. Declara todo a nivel de clase:  

```csharp  
RelativeLayout mainLayout;
TextureView liveView;
PaintCodeButton capturePhotoButton;
```  

Solamente falta instanciarlos y añadirlos a la pantalla, por ejemplo, observa cómo se crea el contenedor y se le añada el control `TextureView`:    

```csharp  
void SetupUserInterface()
{
    mainLayout = new RelativeLayout(Context);
    RelativeLayout.LayoutParams mainLayoutParams = new RelativeLayout.LayoutParams(
        RelativeLayout.LayoutParams.MatchParent,
        RelativeLayout.LayoutParams.MatchParent);
    mainLayout.LayoutParameters = mainLayoutParams;

    liveView = new TextureView(Context);
    RelativeLayout.LayoutParams liveViewParams = new RelativeLayout.LayoutParams(
        RelativeLayout.LayoutParams.MatchParent, 
        RelativeLayout.LayoutParams.MatchParent);
    liveView.LayoutParameters = liveViewParams;
    mainLayout.AddView(liveView);
    
    // Code ommited...

    AddView(mainLayout);
}
```  

Antes de continuar hay otro método que debemos sobreescribir para darle al contenedor su tamaño (y también lo podemos usar para acomodar la interfaz correctamente):  

```csharp  
protected override void OnLayout(bool changed, int l, int t, int r, int b)
{
    base.OnLayout(changed, l, t, r, b);
    if (!changed)
        return;
    var msw = MeasureSpec.MakeMeasureSpec(r - l, MeasureSpecMode.Exactly);
    var msh = MeasureSpec.MakeMeasureSpec(b - t, MeasureSpecMode.Exactly);
    mainLayout.Measure(msw, msh);
    mainLayout.Layout(0, 0, r - l, b - t);

    capturePhotoButton.SetX( mainLayout.Width / 2 - 60);
    capturePhotoButton.SetY(mainLayout.Height - 200);
}
```  

### SetupEventHandlers  
Como dije antes, Android depende mucho de *event listeners* en lugar de  *event handlers*, así que este método es muy sencillo. Necesitamos colocar un manejador de evento al botón que tomará la foto, asi como asignar un escuchador que estará atento al estado del control  `SurfaceTexture` (¿Recuerdas que nuestro *renderer* implementa una interfaz?):  

```csharp  
capturePhotoButton.Click += async (sender, e) =>
{
    var bytes = await TakePhoto();
    (Element as CameraPage).SetPhotoResult(bytes, liveView.Bitmap.Width, liveView.Bitmap.Height);
};
liveView.SurfaceTextureListener = this;
```  

Ah, y otra cosa, vamos a sobreescribir el comportamiento del botón "Atrás" para que funcione para cancelar la fotografía:  

```csharp  
public override bool OnKeyDown(Keycode keyCode, KeyEvent e)
{
    if (keyCode == Keycode.Back)
    {
        (Element as CameraPage).Cancel();
        return false;
    }
    return base.OnKeyDown(keyCode, e);
}
```  

### TextureView.ISurfaceTextureListener implementation  
Ahora toca implementar el núcleo de la página. Comienza por escribir el código para el método `OnSurfaceTextureAvailable` en donde vamos a preparar la salida de la cámara... pero primero necesitamos una cámara, ¿cierto?  
A nivel de clase declara una `Camera`:    

```csharp  
Android.Hardware.Camera camera;
```  
 
Dentro del método abre la cámara (por default tomará la cámara trasera del dispositivo) y obtén sus parámetros. Los necesitamos para elegir el tamaño de previsualización correcto porque queremos que se vea bien en nuestra app:  

```csharp  
camera = Android.Hardware.Camera.Open();
var parameters = camera.GetParameters();
```  

Una vez que tenemos los parámetros a mano, podemos obtener los `PreviewSizes` y de ellos elegir el que mejor se ajusta a nuestras necesidades. En este caso estoy usando una simple <a href="#">expresión linq</a> para obtener el mejor tamaño de acuerdo a la relación de aspecto:  

```csharp  
var aspect = ((decimal)height) / ((decimal)width);

var previewSize = parameters.SupportedPreviewSizes
                            .OrderBy(s => Math.Abs(s.Width / (decimal)s.Height - aspect))
                            .First();

parameters.SetPreviewSize(previewSize.Width, previewSize.Height);
camera.SetParameters(parameters);
```  

Termina estableciendo el valor de `surface` como la textura de previsualización, una vez hecho esto, lo único que queda por hacer es iniciar la cámara:  

```csharp  
camera.SetPreviewTexture(surface);
StartCamera();
```  

Hay otro método en el que debemos escribir código, este es `OnSurfaceTextureDestroyed` en donde debemos detener el uso de la cámara, así que únicamente escribe el código siguiente en él:  

```csharp  
StopCamera();
return true;
```  

### StartCamera and StopCamera  
Este par de métodos son bastante simples también, para `StartCamera` únicamente tenemos que rotar la previsualización para hacer que se vea correctamente en la pantalla (en este caso está establecido para que se vea verticalmente), y terminamos iniciando la cámara:  

```csharp  
camera.SetDisplayOrientation(90);
camera.StartPreview();
```  

El métodod `StopCamera` detiene la previsualización y libera la cámara para que otras aplicaciones puedan acceder a ella:  

```csharp  
camera.StopPreview();
camera.Release();
```  

### TakePhoto
Para tomar una foto, lo que hay que hacer es obtener una imagen fija de lo que se muestra en el video en vivo dentro de la `TextureView`, aquí está el código para hacerlo y regresar los bytes correspondientes:    

```csharp  
camera.StopPreview();
var ratio = ((decimal)Height) / Width;
var image = Bitmap.CreateBitmap(liveView.Bitmap, 0, 0, liveView.Bitmap.Width, (int)(liveView.Bitmap.Width * ratio));
byte[] imageBytes = null;
using (var imageStream = new System.IO.MemoryStream())
{
    await image.CompressAsync(Bitmap.CompressFormat.Jpeg, 50, imageStream);
    image.Recycle();
    imageBytes = imageStream.ToArray();
}
camera.StartPreview();
return imageBytes;
```  

Y así es como después de tanto código podemos hacer uso de la cámara. Sigue leyendo para encontrar un ejemplo de uso:  

## Usage in Forms  

```csharp  
var cameraPage = new CameraPage();
cameraPage.OnPhotoResult += CameraPage_OnPhotoResult;
Navigation.PushModalAsync(cameraPage);  
// ...
async void CameraPage_OnPhotoResult(Pages.PhotoResultEventArgs result)
{
    await Navigation.PopModalAsync();
    if (!result.Success)
        return;
    Image.Source = ImageSource.FromStream(() => new MemoryStream(result.Image));
```  

Si <strong><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage" target="_blank">descargas el código fuente</a></strong> y lo ejecutas, verás algo como esto:   

<iframe width="560" height="315" src="https://www.youtube.com/embed/DgFEK9tPKs0" frameborder="0" allowfullscreen></iframe>



## Acknowledgements   
El código para este post está basado completamente en el código de la <a href="..\Aharphat-Android" target="_blank">CharpHat app</a>,  que a su vez está basado en el código de la app <a href="https://blog.xamarin.com/build-your-own-snapchat-clone-with-xamarin-forms-and-azure/" target="_blank">Moments</a> de <a href="https://github.com/pierceboggan" target="_blank">Pierce Boggan</a>.