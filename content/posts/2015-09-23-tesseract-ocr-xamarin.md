---
layout: post
title: Tesseract OCR in Xamarin
date: 2015-09-23 19:00:00
author: Antonio Feregrino
category: xamarin.forms
summary: While surfing the web for a free / open source solution to a certain OCR problem that I came across I found this pretty cool library named Tesseract OCR which, in its own words, is "probably the most accurate open source OCR engine available".
featured_image: featured.png
lang: en
alias: /tesseract-ocr-xamarin/index.html
tags: XamarinForms, Xamarin
featured_tag: XamarinForms
---

While surfing the web for a free / open source solution to a certain OCR problem that I came across I found this pretty cool library named Tesseract OCR which, in its own words, is *"probably the most accurate open source OCR engine available"*. Along with the library (written in C/C++) there are an entire world of wrappers, including some for Xamarin... so, why not?  

### Application
In this post I'll show you how to create a simple iOS and Android application that allows the user to take a picture and identify the characters that got captured on the picture. Nothing too complicated. It'll be a Xamarin.Forms app, code sharing at its best!  

#### Creating the solution  
For this post I used Xamarin Studio on a Mac, but as you know, you can create it regardless of the OS or IDE. Go to New solution > Cross-platform > App > Blank Xamarin.Forms app. Give it any name you want and select Use Portable Class Library.

<img src="https://thatcsharpguy.github.io/postimages/tesseract-ocr-xamarin/project-creation-1.png" title="Creación del proyecto" />

#### NuGets and NuGets
Then proceed to add the XLabs NuGet package (<code>XLabs.Forms</code>) to the three projects, we'll use the IoC features that it provides and the ability to take photos right from our PCL. Add also the TinyIoC package (<code>XLabs.IoC.TinyIoC</code>) to both the iOS and Android projects, I'm using TinyIoC but you can use whatever DI tool you want.  
  
#### The Tesseract NuGet 
Yay! another NuGet, but this time is the most important for our app. Add the package <code>Xamarin.Tesseract</code> to the three projects, yes, the same package for all three. This little package is <a href="http://shamsutdinov.net/2015/07/01/tesseract-orc-xamarin-part-1/" target="_blank">developed by Artur Shamsutdinov</a> and it is a wrapper for Tesseract OCR that provides a nice API to work with. So far so good.  
  
Tesseract relay on some data files to work, for each language symbols you want to recognize you must add a set of files. For example, this app recognizes english characters so only the <code>eng.*</code> files are needed, these files must be placed in a folder named <code>tessdata</code> inside the AndroidAssets folder for Android and the Resources folder for iOS. You can download the <a href="https://code.google.com/p/tesseract-ocr/downloads/list" target="_blank">symbol files for each language here</a>.

#### Configuring TinyIoC  
As Tesseract and XLabs require platform specific code to work, we must find a way to allow a the shared PCL to execute such code, there are many options but I choose to implement IoC with TinyIoC. To use it we must configure each project separately.  

##### Android
First of all, grab a reference to the container with  
```csharp  
var container = TinyIoCContainer.Current;
```  
The container is like a bucket where we "put" pieces of code that will be used within our application. Then, we must put code in that bucket, we do so by calling the <code>Register</code> method of the container:  
```csharp  
container.Register<IDevice>(AndroidDevice.CurrentDevice);
container.Register<ITesseractApi>((cont, parameters) =>
{
	return new TesseractApi(ApplicationContext, AssetsDeployment.OncePerInitialization);
});
```  
With the first line the device where the app is running gets registered (alongside with all its available features), whereas with the second line we are registering the implementation of the Tesseract API, for Android we must pass in the constructor a reference to the application context where the application is running. For this example it is set to <code>ApplicationContext</code> in the <code>MainActivity.cs</code>.  
Finally we have yet to register our <code>container</code> in the XLabs <code>Resolver</code>:
```csharp  
Resolver.SetResolver(new TinyResolver(container));
```  
By the way, do not forget to add the following <code>using</code> statements at the top of your file
```csharp  
using TinyIoC;
using Tesseract;
using Tesseract.Droid;
using XLabs.Ioc;
using XLabs.Ioc.TinyIOC;
using XLabs.Platform.Device;
```  


##### iOS
It is almost the same process as with Android. First, get a reference to the container:  
```csharp  
var container = TinyIoCContainer.Current;
```  
After that, register the device and the Tesseract API implementation. This time there is no need to add parameters to the <code>TesseractApi</code> constructor.  
```csharp  
container.Register<IDevice>(AndroidDevice.CurrentDevice);
container.Register<ITesseractApi>((cont, parameters) =>
{
	return new TesseractApi();
});
```  
Again, register the <code>container</code> in the <code>Resolver</code>
```csharp  
Resolver.SetResolver(new TinyResolver(container));
```  
Here are the <code>using</code> statements for the <code>AppDelegate.cs</code> file
```csharp  
using TinyIoC;
using Tesseract;
using Tesseract.iOS;
using XLabs.Ioc;
using XLabs.Ioc.TinyIOC;
using XLabs.Platform.Device;
```  

#### Shared code! yay!  
Finally, we get to the code sharing part. The UI for this app wont be much of a hassle, just a button, an image and a label. The button to call the photo-taking action, the image to display the recently captured image and the label to display the recognized text. For this simple demo app all the code will be on the page itself, however, you may want to move the code to a viewmodel to create a more complex app, to start, create a new Page named <code>HomePage</code> in the root of the shared PCL project and add the following as class-level variables:
```csharp  
private Button _takePictureButton;
private Label _recognizedTextLabel;
private Image _takenImage;

private readonly ITesseractApi _tesseractApi;
private readonly IDevice _device;
```  
The last two lines are interfaces which abstract the platform specific features that we registered a few lines above. In the constructor of the <code>HomePage</code> we'll call the <code>Resolve</code> method on our resolver class to get a references to the implementations for each platform:
```csharp  
_tesseractApi = Resolver.Resolve<ITesseractApi>();
_device = Resolver.Resolve<IDevice>();
```  
Then build the UI, I placed the three controls within a <code>StackLayout</code>, and wire the only event:
```csharp  
_takePictureButton.Clicked += TakePictureButton_Clicked;
// ...
async void TakePictureButton_Clicked(object sender, EventArgs e)
{
	if (!_tesseractApi.Initialized)
		await _tesseractApi.Init("eng");

	var photo = await TakePic();
	if (photo != null)
	{
		var imageBytes = new byte[photo.Source.Length];
		photo.Source.Position = 0;
		photo.Source.Read(imageBytes, 0, (int)photo.Source.Length);
		photo.Source.Position = 0;

		_takenImage.Source = ImageSource.FromStream(() => photo.Source);
		var tessResult = await _tesseractApi.SetImage(imageBytes);
		if (tessResult)
		{
			_recognizedTextLabel.Text = _tesseractApi.Text;
		}
	}
}
// ...
 private async Task<MediaFile> TakePic()
{
	var mediaStorageOptions = new CameraMediaStorageOptions
	{
		DefaultCamera = CameraDevice.Rear
	};
	var mediaFile = await _device.MediaPicker.TakePhotoAsync(mediaStorageOptions);

	return mediaFile;
}
```  

#### Wrapping up
Here are some photos I took of the app running on an iPod touch:
<blockquote class="imgur-embed-pub" lang="en" data-id="a/FFi7b"><a href="//imgur.com/FFi7b">Tesseract OCR in Xamarin</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

And that's pretty much it. Now your Xamarin.Forms app can see! but everything has its drawbacks... this works well as a proof of concept but I have some final thoughts on it:  

 - You may want to perform some preprocessing techniques on the image to improve the accuracy of Tesseract's results  
 - Be aware of the size of your app as *tessdata* files aren't small  
 - Consider the memory of the device yout app will be running on.  
 
 As always, get the code for [Xocr on GitHub](https://github.com/fferegrino/xocr) and if you have questions, let me know!