layout: post
title: Full page camera in Xamarin.Forms
date: 2016-11-08 08:00:00
author: Antonio Feregrino
excerpt: Learn how to take photos from your Xamarin.Forms app using a custom page renderer to use the camera without leaving your application, with this control, you can create your own camera user interface to match the design of your app.
featured_image: featured.jpg
images_folder: /xamarin-forms/camerapage/
github: https://github.com/ThatCSharpGuy/Forms-FullCameraPage
lang: en
tags: XamarinForms, Xamarin
featured_tag: XamarinForms

It has been a while since I coded <a href="..\Aharphat-Android" target="_blank">CharpHat</a>, which is an app that lets you snap a picture of anything and then put a nice C# graduation cap on it. That app was far from perfect, but it helped me to practice the usage of custom page renderers.  

Today I decided to retake that project, but this time trying to isolate the code needed to build the interface and funcionality of the page, so that anyone looking forward to implement a full camera page in their apps could reuse the code for their own projects. So be sure to <strong><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage" target="_blank">grab the source code</a></strong> for this post.

## Forms abstractions  
<small><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage/blob/master/CameraPage.cs" target="_blank">Here is the source code for this section.</a></small>  

Let's start by creating the Xamarin.Forms page that will serve as our point of interaction with the custom code:  

```csharp  
public class CameraPage : ContentPage
{
    public delegate void PhotoResultEventHandler(PhotoResultEventArgs result);
    public event PhotoResultEventHandler OnPhotoResult;
```  

Business as usual, create a class deriving from `ContentPage`. I have added an event handler as I want to access the picture taken by the user. Now let's throw in some methods to call whenever an user performs an action in our camera page (in this case, the user will be allowed to take a photo or cancel the action):

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

For reference, see the properties inside the `PhotoResultEventArgs` class:

```csharp  
public bool Success { get; private set; }
public int Width { get; private set; }
public int Height { get; private set; }
public byte[] Image { get; private set; }
```  

Now, time to move on to the platform specifics. 

## In Xamarin.iOS    
<small><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage/blob/master/iOS/CameraPageRenderer.cs" target="_blank">Here is the source code for this section.</a></small>  

To be honest, this implementation is the easiest by far. Start off by creating a class that inherits from `PageRenderer`, and to add the `ExportRenderer` attribute:

```csharp  
[assembly: ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace FullCameraPage.iOS
{
	public class CameraPageRenderer : PageRenderer
```  

Now, an this is very important, you need to override the `ViewDidLoad` method, since it gets called as soon as our page is loaded by the iOS mechanisms. For the sake of organisation let's split the code in several other methods:  

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
As the name states, here is where you need to build the UI. As you may have guessed, it is all done by code, but don't worry, it is very easy... as long as your UI isn't so complex, but you can do whatever you need here.  

For this sample the UI will consist of a couple of buttons and a surface where the live preview from the camera is going to be shown, so you need to declare them on a class-level scope:  

```csharp  
VectorButton takePhotoButton;
VectorButton cancelPhotoButton;
UIView liveCameraStream;
```  

To set the items in place you need to think as if you were working with a relative layout, meaning that you need to set the position of each item within the screen. For example, look at how the live camera preview view is positioned:

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
Now that the UI has been built, let's hook up the event handlers to each control, luckly for this sample there are only two buttons on screen: one to take the picture and the other to cancel the whole thing.

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

The property `Element` contains a reference to the page associated to the renderer, and is our way to interact with our Forms project. As for the method `CapturePhoto`... we'll see it later. 

## AuthorizeCameraUse  
Now it's time to ask the user for its permission to access the camera: 

```csharp  
var authorizationStatus = AVCaptureDevice.GetAuthorizationStatus(AVMediaType.Video);
if (authorizationStatus != AVAuthorizationStatus.Authorized)
{
    await AVCaptureDevice.RequestAccessForMediaTypeAsync(AVMediaType.Video);
}
```  

But wait a minute, before executing the code above, make sure you have added the key `Privacy - Camera Usage Description` to the Info.plist in your project.  

### SetupLiveCameraStream
Now the "heavy" stuff.  
  
Start by declaring at class-level scope an `AVCaptureSession`, `AVCaptureDeviceInput` and `AVCaptureStillImageOutput`, as they will hel us access the camera, display the live feed and capture the photo.  

Then, inside the `SetupLiveCameraStream` method, initialize the capture session, create a preview layer with the same size as our `liveCameraStream`, and add it as a sublayer of it:

```csharp  
    captureSession = new AVCaptureSession();
    var videoPreviewLayer = new AVCaptureVideoPreviewLayer(captureSession)
    {
        Frame = liveCameraStream.Bounds
    };
    liveCameraStream.Layer.AddSublayer(videoPreviewLayer);
```  

Next, "create" a capture device (you can configure it to work according to your needs). And then, from it create the an input source for the capture session:

```csharp  
    var captureDevice = AVCaptureDevice.DefaultDeviceWithMediaType(AVMediaType.Video);
    ConfigureCameraForDevice(captureDevice);
    captureDeviceInput = AVCaptureDeviceInput.FromDevice(captureDevice);
```  

We have an input (the camera of the device), now we need an output which is going to be a jpeg photograph:

```csharp  
    var dictionary = new NSMutableDictionary();
    dictionary[AVVideo.CodecKey] = new NSNumber((int)AVVideoCodec.JPEG);
    stillImageOutput = new AVCaptureStillImageOutput()
    {
        OutputSettings = new NSDictionary()
    };
```  

Finalize by setting the input and output of the capture session and starting it: 

```csharp  
    captureSession.AddOutput(stillImageOutput);
    captureSession.AddInput(captureDeviceInput);
    captureSession.StartRunning();
```  

### CapturePhoto
At last, the icing on the cake, the code to capture the photo. The code is pretty simple: Take the output and capture a still image from it, as we only need the bytes we get an `NSData` containing the taken photo. 

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
<small><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage/blob/master/Droid/CameraPageRenderer.cs" target="_blank">Here is the source code for this section.</a></small>    
This implementation isn't as clean as it is in iOS. Mainly because Android puts a lot of ephasis in the use of listeners, rather than in event handlers. However, that is not a problem for us.

As with the iOS implementation, start by creating a new class and make it derive from `PageRenderer` and also make it implement the `TextureView.ISurfaceTextureListener` interface. Don't forget the `ExportRender` attribute:

```csharp  
[assembly: Xamarin.Forms.ExportRenderer(typeof(CameraPage), typeof(CameraPageRenderer))]
namespace FullCameraPage.Droid
{
    public class CameraPageRenderer : PageRenderer, TextureView.ISurfaceTextureListener
```  

Then, override the `OnElementChanged` method (if you have creaated custom renderers before this method may be familar to you), this method is going to be called everytime the a `CamerPage` is shown on screen:

```csharp  
protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.Page> e)
{
    base.OnElementChanged(e);
    SetupUserInterface();
    SetupEventHandlers();
```  

### SetupUserInterface  
In this method we are supposed to create the camera page itself, you can do it by creating an *axml* file and calling all the Android inflating stuff... Or, like in this sample, you can create it by code.

For this sample, we'll need a `RelativeLayout` to work as a container, a `TextureView` to display the live feed from the camera, and a `Button` (a `PaintCodeButton` actually) to snap the photograph. Declare all them at class-level scope: 

```csharp  
RelativeLayout mainLayout;
TextureView liveView;
PaintCodeButton capturePhotoButton;
```  

Now, proceed to create them and add them to the screen, for example, see how we can create the container layout and add the `TextureView` to it:  

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

Before continuing, there is another method (`OnLayout`) we need to override to give our main layout it's size (and acommodate the UI accordingly): 

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
As I said, Android relies mostly on event listeners rather than handlers, so the code for this method is pretty simple. We need to set an event handler for the "sutter" button and assign the listener that will be aware of the `SurfaceTexture` status (remember that our page render implements an interface?):  

```csharp  
capturePhotoButton.Click += async (sender, e) =>
{
    var bytes = await TakePhoto();
    (Element as CameraPage).SetPhotoResult(bytes, liveView.Bitmap.Width, liveView.Bitmap.Height);
};
liveView.SurfaceTextureListener = this;
```  

And one more thing, let's to override the default behavior of the "back" button, so that it acts as a cancel button for the camera:

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
Now is time to implement the core of our page. Start by writing the code for the `OnSurfaceTextureAvailable` where we will prepare the output for the camera, but first we'll need a camera, right?

At class-level scope declare a `Camera`:  

```csharp  
Android.Hardware.Camera camera;
```  
 
Inside the method, open the camera (by default it'll try to open the back camera of the device) and get its parameters. We need them to select the right preview size, because we want things to look great in our app:

```csharp  
camera = Android.Hardware.Camera.Open();
var parameters = camera.GetParameters();
```  

Once we have the parameters at hand, we can get the avaliable `PreviewSizes` and get the one that fits our preview surface. In this case I'm using a simple <a href="#">linq expression</a> to get the best preview size based on aspect ratio:

```csharp  
var aspect = ((decimal)height) / ((decimal)width);

var previewSize = parameters.SupportedPreviewSizes
                            .OrderBy(s => Math.Abs(s.Width / (decimal)s.Height - aspect))
                            .First();

parameters.SetPreviewSize(previewSize.Width, previewSize.Height);
camera.SetParameters(parameters);
```  

Finish by setting our surface as the preview texture, at this point the only thing left to do is to start the camera:

```csharp  
camera.SetPreviewTexture(surface);
StartCamera();
```  

The other method we need to write code into is `OnSurfaceTextureDestroyed` in order to stop the camera, so just write the following inside and it'll be all:  

```csharp  
StopCamera();
return true;
```  

### StartCamera and StopCamera
These two methods are quite simple too, for `StartCamera` we only need to rotate the preview to make it look right in the screen (in this case I'm setting it to be viewed vertically), and then finally, start the camera:   

```csharp  
camera.SetDisplayOrientation(90);
camera.StartPreview();
```  

The `StopCamera` method stops the preview and releases the camera, so that other apps can access to it:   

```csharp  
camera.StopPreview();
camera.Release();
```  

### TakePhoto

In order to get a photo, the only thing we need to do is get an sitll image from the live feed presented in the `TextureView`, here is the code to do so and then return the image in bytes:

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

And that's it, after all that code, you can now make use of this camera page. Keep reading to find a sample usage code:

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

If you <strong><a href="https://github.com/ThatCSharpGuy/Forms-FullCameraPage" target="_blank">download the source code</a></strong> and run it, you will see something like this:

<iframe width="560" height="315" src="https://www.youtube.com/embed/DgFEK9tPKs0" frameborder="0" allowfullscreen></iframe>



## Acknowledgements 
The code for this post was entirely based on the code from the <a href="..\Aharphat-Android" target="_blank">CharpHat</a>, which at the same time was based on the <a href="https://blog.xamarin.com/build-your-own-snapchat-clone-with-xamarin-forms-and-azure/" target="_blank">Moments app</a> by <a href="https://github.com/pierceboggan" target="_blank">Pierce Boggan</a>.