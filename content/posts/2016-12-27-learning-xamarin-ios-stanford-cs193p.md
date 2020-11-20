---
layout: post
title: Learning Xamarin.iOS at Stanford
date: 2016-12-27 08:00:00
author: Antonio Feregrino
excerpt: Here is my experience learning Xamarin.iOS by taking the Stanford's CS193P course online which is given for Swift language.
featured_image: stanford.jpg
images_folder: /xamarin-ios/
github: https://github.com/fferegrino/cs193
lang: en
tags: Xamarin, XamariniOS
featured_tag: Xamarin
---

To finish the year in a good manner I decided to ramp up my Xamarin.iOS development skills. So far I had only worked on iOS apps using Xamarin.Forms and I have only made small apps following Apple tutorials <a href="https://developer.apple.com/library/content/referencelibrary/GettingStarted/DevelopiOSAppsSwift/" target="_blank">like this</a>, but for me that is not enough, I wanted to take a course like I did a few years ago when I took one on Android dev, with that in mind <a href="https://twitter.com/io_exception/status/807047846063177728" target="_blank">I asked on Twitter</a> whether NSScreencasts was a good choice for novices.

Luckily I got a several answers of all kind, generally the answers where a big yes, but not for beginners (which I consider myself), a couple of them suggested some books, but three suggested I should took a free Stanford Course with the nuance that it was given using Swift 2, to me it isn't a huge deal as my main focus is to learn the workflow and essentials of iOS dev, and then transfer that knowledge to Xamarin.iOS.

I'm about 3 lectures away from finishing the course, but in this post I'll share my experience so far:

## Syntax differences  
This may be because I am a .NET developer since 2008 but, mate: Swift syntax is so weird! and my guess is that is in great part thanks to Obj-C... but anyway, the differences for basic properties, methods, base classes, aren't that big. You can get around them thanks to the great intellisense offered by Xamarin/Visual studio since they are mostly only casing (`tableView` vs `TableView`) distinctions, if you want, you can read my post on basic <a href="../xamarin-ios-vs-traditional" target="_blank">Xamarin.iOS vs Swift differences</a>.

In other cases you'll have to look for the arguments and return type of a given method to be 90% that you are calling the right method (the other 10% comes from executing and see if it works as expected).

## Enums whith associated values  
WAIT WHAT? really? wow, there are no such things when using C#... you'll have to work around this issue, C# do have enums but they're *masked* integer types and no other info can be associated with them.

What I ended up doing to overcome this issue for the *Calculator assignment* was to create <a href="https://github.com/fferegrino/cs193/blob/master/Calculator/CalculatorBrainDictionaries.cs" target="_blank">a few dictionaries</a>. It got a bit messy, but in the end it worked pretty good.

## Closures  
Closures in Swift are our good (not so) old Func and Action types in C#. You don't get the nice $1, $2 syntax, but who needs that? A thing to note is that in Swift you the possibility to specify how a local variable will be referenced inside a closure, in order to avoid memory leaks.

## Of vars and properties  
In Swift exist properties which are almost, almost like properties in C#, you can see this when using the "stored properties":

```swift  
class AxesDrawer
{
    var color = UIColor.blueColor()
    var minimumPointsPerHashmark: CGFloat = 40
```  

Which are somewhat like the "automplmemented properties" in C#

```csharp  
class AxesDrawer 
{
    public UIColor Color { get; set; }
    public CGFloat MinimumPointsPerHashmark { get; set; }
```  

In both languages you can have computed properties:  

```swift  
// Swift
class AxesDrawer 
{
    private var _color : UIColor = UIColor.blueColor()
    var color : UIColor { 
        get { 
            return _color 
        } 
        set { 
            _color = newValue 
        } 
    }
```  

```csharp  
// C#
class AxesDrawer 
{
    UIColor _color = UIColor.Blue;
    public UIColor Color 
    { 
        get { return _color; } 
        set { _color = value; } 
    }
```  

Another cool feature that I would enjoy in C# are the property observers (`willSet` and `didSet`) which behavior we can mimic in C# using computed properties but the code does not look as clean as with Apple's language:

```csharp  
public double _scale = 0.9;
public double Scale
{
    get { return _scale; }
    set 
    { 
        // willSet code
        _scale = value;
        // didSet code 
    }
}
```  


## Implementing protocols
When implementing protocols, there is a weird thing that happens: At first I thought that Protocols in Swift where like Interfaces in C#... turns out they are not! When using protocols there are some methods or properties that you are not obligated to implement whereas when using an interface you are required to implement all the members defined within it, so the guys at Xamarin came up with this weird solution where you need to implement an *EMPTY* interface that has nothing but metadata that you can use to mimic the protocols behavior (<a href="https://developer.xamarin.com/api/type/MonoTouch.UIKit.IUITextViewDelegate/" target="_blank">see here</a>). For example, take a look at the approach to create a delegate for the UITextField and respond to a protocol's member call:

```csharp  
public partial class TweetTableViewController : UITableViewController, IUITextFieldDelegate
{
    // ...

    [Export("textFieldShouldReturn:")]
    public bool ShouldReturn(UITextField textField)
    {
        // Delegate code
```  

## Swift's extensions to types  
When it comes to extend the capabilities of classes, Swift offers a more range of movement than C# since you can add properties, constructors and make a class adopt a protocol. C# only allows to "extend" vía static methods, no other members can be added to a given type.

## Using the iOS interface designer  
Neither the Android designer nor the iOS designer are perfect in their current form, and as such I think it is awesome that you can use Xcode to create your UI and then have it sync and map every outlet and action in your C# project within Xamarin Studio. What you need to do is to *right click* on your storyboard file and select `Open with Xcode interface builder`.

Every time you save your interface in Xcode it'll be synced to Xamarin Studio without issues.

The Xamarin approach will create a WhateverViewController.designer.cs containing a partial definition of your `UIViewController` derivate class, in it all the outlets and actions of your view controller will be stored, each of them with a certain attribute (for outlets the attribute is `[Outlet]`) that tells the designer what they are used for. You are not supposed to mess with this file, however, if you need to take an outlet out of there you can do so... as long as you place it somewhere else without the designer attribute, as I did when I had to perform an action as soon as a view was assigned:  

```csharp  
FaceView _faceView;
[Outlet]
protected FaceView FaceView
{
    get { return _faceView; }
    set
    {
        _faceView = value;
        // didSet:
        var gesture = new UIPinchGestureRecognizer();
        gesture.AddTarget(() => _faceView.ChangeScale(gesture));
        // Ommited for brevity ...
        UpdateUI();
    }
}
```  

Despite the fact of that you must be switching between the Xcode designer and Xamarin's IDE you should worry no more, you are in good hands when it comes to design the interface of your app. 

I do have a complaint about the integration and that is because at the moment I was taking the course it was impossible to open a .storyboard file modified with Xcode 8 in Xamarin Studio, but I think they're addressing this problem.

## Custom views  
Just as you can create custom controls using Swift or Obj-C you can create custom views using C#, there are a few things to take into consideration, though:  

Make sure that your custom view implements a public constructor that takes an `IntPtr` instance as a parameter:

```csharp  
public FaceView (IntPtr handle) : base (handle)
{
}
```  

Also, make sure to register your custom views to be visible at design time in the editor, using the appropiate attributes `Register` and `DesignTimeVisible`:

```csharp  
[Register("FaceView"), DesignTimeVisible(true)]
public class FaceView : UIView
{
```  

If you want your properties to be available for modification at design time you will need to mark them with yet another couple of attributes (`Export` and `Browsable`):

```csharp  
[Export("Color"), Browsable(true)]
public UIColor Color
{ // ...
```  

## CoreData  
Uh, for what I've been reading CoreData isn't that easy to use with Xamarin... yet it is possible. I didn't try to do the assignments, mainly because I find it too specific for my purposes at the moment. If you want to try, maybe this links points to a sample that uses this feature in Xamarin: <a href="https://developer.xamarin.com/samples/monotouch/ThreadedCoreData/" target="_blank">ThreadedCoreData sample</a>.

## My code  
In case you are interested, you can see the code I created & "translated" from some of the on class demos and assignments for this course in this <a href="https://github.com/fferegrino/cs193" target="_blank">GitHub repo</a> feel free to reach to me in case of doubts.

## So... where?  
You can see the lectures from <a href="https://itunes.apple.com/mx/course/developing-ios-9-apps-swift/id1104579961?l=en" target="_blank">iTunes U</a> or <a href="https://www.youtube.com/watch?v=_lRx1zoriPo&list=PLsJq-VuSo2k26duIWzNjXztkZ7VrbppkT" target="_blank">YouTube</a>.
  
## To Paul Hegarty  
I'm 99.9999% sure that you won't read this, but just in case you do: I want to thank you for this lecture, you cannot imagine the knowledge I have gained by simply sitting in front of a PC listening to you, following your code demos and doing the assignments, even using Xamarin. Also thanks to Stanford University for providing this course for free (even though I'd pay for it, seriously!)
