---
layout: post
title: Opening files in Xamarin.Forms
date: 2016-01-08 21:00:00
author: Antonio Feregrino
summary: Learn how to enable your application to receive files from other apps in the same device.
lang: en
featured_image: featured.png
github: https://github.com/ThatCSharpGuy/xf-file-receiver
tweet_id: 685699164018065408
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

Do you want your app to receive files from other applications?  

Let's set a practical example: I built a simple [markdown viewer in Xamarin.Forms](/MrkViewer) and what I wanted my app to do was to show up in the available options when a `.md` file were about to be opened. This is a pretty straightforward task when developing platform specific projects... but when using Xamarin.Forms you may not know where to start, to be honest, I wasn't.  
  
### Starting point
First fo all, remember that **all Xamarin.Forms apps are nothing but native apps**, there is no magic regarding them. They all start in the same point whether it is a MainActivity for Xamarin.Android, an App.xaml for Windows Phone or an AppDelegate form Xamarin.iOS and when dealing with incoming files the same applies. So, with that in mind, let's start.

## Xamarin.Forms  
Let's create a sample application that will show the name and content of the received file, just create class to hold the data, for this sample it'll only contain a `Name` and `Content` property, but you can extend it later to fit your needs. 

```csharp  
public class IncomingFile
{
	public string Name { get; set; }
	public string Content { get; set; }
}
```  

Next, create a page (in this sample `FilePage.xaml`) with two labels (for the file name and file content), give them a name and add the next method in the code behind: 

```csharp  
public void SetIncomingFile(IncomingFile file)
{
	FileName.Text = file.Name;
	FileContent.Text = file.Content;
}
```  

Then, in your Forms app starting point (generally App.cs) create a property of type IncomingFile, don't just use an autoimplemented property, create a backing field for it. Somewhat like this:
```csharp  
private IncomingFile _incomingFile;

public IncomingFile IncomingFile
{
	get { return _incomingFile; }
	set 
	{
		_incomingFile 	= value; 
		(MainPage as FilePage).SetIncomingFile (_incomingFile);
	}
}
```  

Notice how everytime the IncomingFile property is modified, the method `SetIncomingFile` of `FilePage` is called. We will be using this property to set the file from within each platform code. So, let's dive right into it.

## Windows 8.1  
I'll start with Windows 8.1, since it was my main target with MrkViewer, check the getting started on <a href="https://developer.xamarin.com/guides/cross-platform/xamarin-forms/windows/" target="_blank">how to create a Windows App</a> for more info. I won't cover Windows Phone or UWP apps because they are similar to Win 8.1.
  
First of all: you must create a file association inside your application manifest, double click the `appxmanifest` file, a window will open, from there select the Declarations tab and using the dropdow list select `File Type Associations` and click add.  


<div class="pure-g">
<div class="pure-u-lg-1-3">
<img src="https://thatcsharpguy.github.io/postimages/opening-files-xamarin-forms/appxmanifest.png" title="The appxmanifest file" />
</div>
<div class="pure-u-lg-1-3">
<img src="https://thatcsharpguy.github.io/postimages/opening-files-xamarin-forms/file-assoc.png" title="Add new File Type Association" />
</div>  
</div>
<br />
After clicking `Add` a form will appear to the right, we'll keep things simple. Fill in the form the following fields:  

 - **Display name**: Markdown
 - **Name**: md
 - **File type**: .md (don't forget the leading dot)

And that's about it. Next, we'll have to get the file when our app is launched for that purpose.  
  
In Windows 8.1 different methods are called depending on what kind of event launched our app, when our app is opened as the result of a File Type Association, the starting poit of the app will be the `OnFileActivated` method inside the `App` class. Let's override it: 

```csharp  
protected override void OnFileActivated(FileActivatedEventArgs e)
{
    base.OnFileActivated(e);
    // ...
```  
  
The reference to the recently opened file is inside the `FileActivatedEventArgs`, to find it use something like this:

```csharp  
StorageFile file = null;
if (e.Files.Count == 1)
{
    file = e.Files[0] as StorageFile;
}
```  

Launching an app to receive a file is not so different than launching it normally, so after we get the reference to the desired file, we must let the launching process continue as usually, for that copy the code inside the `OnLaunched` from `Frame rootFr...` to `if (rootF...`  and paste it after the code above.

```csharp  
    file = e.Files[0] as StorageFile;
} //

Frame rootFrame = Window.Current.Content as Frame;
if (rootFrame == null)
{
    rootFrame = new Frame();
    rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
    rootFrame.NavigationFailed += OnNavigationFailed;

    Xamarin.Forms.Forms.Init(e); // <- DON'T FORGET TO INITIALIZE FORMS

    Window.Current.Content = rootFrame;
}
```  
  
Now all we have to do is to navigate to the generated `MainPage` passing the file as a navigation parameter:

```csharp  
// Pass the recently opened file ----vvvv
rootFrame.Navigate(typeof(MainPage), file);
Window.Current.Activate();
```  

As you may guess, there is still work to do inside the Windows 8 generated  `MainPage`, and that's true. We still need to get the file passed as navigation parameter. First of all, instead of calling the forms app in this way `LoadApplication(new YOUR_NAMESPACE.App());`, create a field of type `new YOUR_NAMESPACE.App`, like

```csharp  
FileReceiver.App _app;
```  

And then call the `LoadApplication` using that field

```csharp  
_app = new FileReceiver.App();
LoadApplication(_app);
```  

Now the final step: override the `OnNavigatedTo` method inside the `MainPage` class, but we need to be extra careful here, since this method will be called regardless of how was the app launched, so take that in consideration:

```csharp  
protected override async void OnNavigatedTo(NavigationEventArgs e)
{
    StorageFile storageFile = e.Parameter as StorageFile;
    if (storageFile != null)
    {
        string content = await FileIO.ReadTextAsync(storageFile);
        var incomingFile = new IncomingFile
        {
            Name = storageFile.DisplayName,
            Content = content
        };
        _app.IncomingFile = incomingFile;
    }
    base.OnNavigatedTo(e);
}
```  

<br />

## Android
As with Windows, in Android you must tell the OS that your app is ready to receive a certain kind of files, and to do so, you must edit the `AndroidManifest.xml` file. Remember that for Xamarin.Android there are two ways to work with the manifest:

 - Editing the XML
		
```xml  
<activity  android:icon="@drawable/icon" android:label="FileReceiver"  name="Something.MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="file" />
        <data android:mimeType="*/*" />
        <data android:pathPattern=".*\\.md" />
    </intent-filter>
</activity>
```  
		
 - Using class level attributes
		
```csharp  
[Activity(Label = "FileReceiver",
        MainLauncher = true, Icon = "@drawable/icon")]
    [IntentFilter(
        new[] { Intent.ActionView },
        Categories = new[]
        { 
            Intent.CategoryDefault,
            Intent.CategoryBrowsable,
        },
        DataScheme = "file",
        DataMimeType = "*/*",
        DataPathPattern = ".*\\.md"
    )]
```  

For this sample let's take the attribute approach. Then, once the `MainActivity.cs` file is open, separate the `LoadApplication` method and the instantiation of the Forms app: 

```csharp  
var application = new App ();

LoadApplication (application);
```  

In Android, every time an app is launched to receive a file, the `OnCreate` method is executed as if it were launched manually by the user, no special method is called or something, to know if the app was launched to receive a file we'll have to look at the Activity's Intent action and type:  

```csharp  
string action = Intent.Action;
string type = Intent.Type;

if (Intent.ActionView.Equals(action) && !String.IsNullOrEmpty(type))
{
	// This app was launched to receive a file ...
```  

After we are sure that the app was to receive a file, we can safely get an `Uri` to the file by getting the Intent data, reading it and then creating an instance of the previously defined `IncomingFile` class:  

```csharp  
Android.Net.Uri fileUri = Intent.Data;

string fileContent = File.ReadAllText(fileUri.Path);
string fileName = fileUri.LastPathSegment;

var incomingFile = new IncomingFile { Name = fileName, Content = fileContent }; 
```  

We have finally handled a file receiving within the Xamarin.Android project, the last thing we have to do is to set the `IncomingFile` property of the Forms app, a call to `application.IncomingFile` will do the trick:  

```csharp  
application.IncomingFile = incomingFile;
```  

<br />

## iOS  
We already know that we need to register our app somehow to let the os know about the existence of our app, and in iOS the wa to do this is vía the `Info.plist` file. When I started  to implement this I struggled a bit because I think the docummentation a bit too vague.  
  
Any way, in your app, open the Info.plist file, and at the bottom part click on the `Advanced tab`, now, we have to fill in some information about the type of files we want to open in our app, for Markdown files we must add a new *Document Type* with the following values:  
  
 - **Name**: Markdown document
 - **Types**: net.daringfireball.markdown  
  
Now, since `net.daringfireball.markdown` is not a known type for iOS, we have to register it in the same UI we declared the filetype association, click add in *Exported UTIs*, once available, put this values on the text fields:  
  
 - **Description**: Markdown document
 - **Identifier**: net.daringfireball.markdown
 - **Conforms To**: public.plain-text  

There is a few lines we need to add in order to make this work, like adding `.md` as a file extension for the files... just to be sure, open the `Info.plist` with an XML editor and make sure it looks <a href="https://github.com/fferegrino/xf-file-receiver/blob/master/FileReceiver.iOS/Info.plist#L52" target="blank">somewhat like this in code</a> or take a look at the following image.

<img src="https://thatcsharpguy.github.io/postimages/opening-files-xamarin-forms/file-assoc-ios.png" title="Add new File Type Association in iOS" />
  
Now, the most fun part, the code.  
  
As with the Windows version of our app, we need to separate the call to `LoadApplicaton` from the instatiation of the Forms app, again, create a Field inside the `AppDelegate` class of type `new YOUR_NAMESPACE.App`, like:

```csharp  
FileReceiver.App _app;
```  

Now, inside the `FinishedLaunching` method, instantiate the `_app` field and call `LoadApplication` using it as a parameter:
```csharp  
_app = new App ();
LoadApplication(_app);
```  

When our app is launched to open a file, a method is called in the `AppDelegate` class, it's name is `OpenUrl`. In order to handle the icoming file, we must override it. The method receives an `NSUrl` as an argument, and that is the url of our file.  
  
I will use the `File.ReadAllText` static method inside the `System.IO` to get the content of the app that has to be opened by the app, such method takes a file path (in the form of string) as a parameter and returns the text contained by that same file.  
  
Be careful here because there is a catch with the url you are given inside your method: it has the protocol `file://` at the beginning of the url, so you need to remove it before sending the path to the `ReadAllText` method, in this case, I just trimmed the first 7 characters of the url:

```csharp  
// Remove "file://" at the beginning of the url:
string filePath = url.AbsoluteString.Substring(7);
```  
  
Now it is safe to read all the text inside the file and pass it to the Forms application:

```csharp  
// Get the file name
var fileName = url.PathComponents [url.PathComponents.Length - 1];

var fileContent = System.IO.File.ReadAllText (filePath);
_app.IncomingFile = new IncomingFile 
{
    Name =  fileName,
    Content = fileContent
};
```  

In iOS you are responsible to clean all the files that your app opens in this way, my recommendation is that you delete the file after reading it.  
  
## Wrapping up  
As you can see, it is not hard to enable your Xamarin.Forms app to open certain kind of files, all you have to do is tell the operating system (through configuration files) that your app is capable of opening that kind of files, and then write some code at the beginning of your app to handle the incoming file. 