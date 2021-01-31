---
layout: post
title: Introduction to Xamarin.Forms (Sharpuladora app)
date: 2015-12-16 18:00:00
author: Antonio Feregrino
categories: c-sharp
summary: This guide will take you step by step to create your first Xamarin.Forms app.
featured_image: featured.png
lang: en
github: https://github.com/fferegrino/sharpuladora
pdfguide: https://github.com/fferegrino/sharpuladora/releases/download/p1.0.1/guia.pdf
codeguide: https://github.com/fferegrino/sharpuladora/releases/latest
tags: Xamarin, XamarinForms, Guia
featured_tag: XamarinForms
---

Who hasn't made a classic "Hello world!" while getting started with a new language or framework?  
  
Well, as classic as the "Hello world!" is to code a simple calculator, and for Xamarin.Forms it is not the exception. In this guide I'll show you how to use some controls to build your apps... including your very own calculator!

# How does this guide work? 
This guide uses an interactive format, it comes with code that you can test and modify as you do the exercise. The code is divided in three or four folders:  

 - **start** is the starting point, contains a solution ready to be used to work with.
 - **parts** contains each and every needed file to complete the guide, usually these are provided for you to use them as reference if you ever get stuck on something, of course you can copy the code. As this document is divided in *parts* to which a subfolder corresponds.   
 - **final** is the final result, the place we want to reach with this guide. This folder, as the last one, is included as reference, to show the result of this guide.  

# La Sharpuladora

## Introducción  
This guide assumes that you have a basic understanding of how Xamarin works, if you dont, I encourage you to take a look to [Getting started with Xamarin](https://developer.xamarin.com/guides/cross-platform/getting_started/). My post on [what do you need to develop in Xamarin (in spanish)](http://thatcsharpguy.com/post/xamarin-como-empiezo/) or this other about [how to go from console to mobile](http://thatcsharpguy.com/post/from-console-to-mobile/).

## Part 1 - Getting familiarized with the code  
As every IDE does, both Xamarin Studio and Visual Studio have their own ways of managing the files that make our app work. But to summarize: these environments divide the projects per platform, so, as we want our calculator to be available in the three main mobile platforms we will have three project plus another one which is the core of the app. I wrote a post about this, if you want to know more, check: [How is my code organized in Visual Studio?](http://thatcsharpguy.com/post/code-organization-visual-studio/).  
  
Now is time to get our hands into the code, open up the solution `Sharpuladora.sln` that is inside the `start` folder and look a the panel in the solution explorer. For this part of the guide there is no code to add but there is code to look. Go ahead and explore, browse the solution. Once you have had enough, move on to part 2.

## Part 2 - The pages
The graphic interface of the apps built with Xamarin.Forms are composed by a set of elements of type `Page`, you can build your own pages from scratch, however, Forms offers to us some pre-coded page templates that we can use to work with in our apps. You can see a full list of those template pages <a href="https://developer.xamarin.com/guides/cross-platform/xamarin-forms/controls/pages/" target="_blank">here</a>.  

The *Sharpuladora app* consists of only one page, created using a `ContentPage` since it is one of the easiest to use.  

### Part 2.1 - Creating the main page  
When it comes to create a new page, we have to decide whether we will create using pure C# code or use XAML, this time, we will use C#.  
  
Create a new folder and name it  `Pages` inside the project named  `Sharpuladora`, then create a new C# class named  `CalcPage` inside that new folder. Make your `CalcPage` derive from  `ContentPage`.  

##### Code
File: `CalcPage.cs` 

### Part 2.2 - Setting a start page    
It is always important to set a start page for our app and in the case of Xamarin.Forms it has to be specified through a property. That property is named `MainPage` and can be found in the `App` class (inside `App.cs`).  
  
In the solution inside  `sart` the property `MainPage` is set with a page created in-place, we must change that for a reference to our `CalcPage`:  

```csharp  
MainPage = new CalcPage();
```  

##### Code
File: `App.cs`

## Part 3 - The layout  
Our page works as a container for the interface elements, however we must add a layout to position those elements in the page. As with the pages, Forms has a set of layouts out of the box, <a href="https://developer.xamarin.com/guides/cross-platform/xamarin-forms/controls/layouts/" target="_blank">see all here</a>.  
  
### Part 3.1 - The Grid
A simple calculator does not require of a lot of thought, the buttons are sorted in rows and columns, and that seems like a job for a `Grid`.  
  
A `Grid` is made of rows and columns which in Xamarin.Forms are specified in the form of `RowDefinition`s o `ColumnDefinition`s. Look at the following code:  
  
```csharp  
var layout = new Grid();

// Rows:
layout.RowDefinitions.Add(new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
layout.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star) });

// Columns:
layout.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star) });
layout.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star) });
```  

In the code above we create a new `Grid` called `layout` and then it gets added a pair of rows and columns. Something to notice here is that a `Width` is specified for the columns and a `Height` is specified for the rows. The fact that we use `GridUnitType.Star` (or `*`) indicates that the size of the columns will be proportional to each other.  
  
For this excerise, create a grid with rows and columns that represent the following figure:   

<img src="https://thatcsharpguy.github.io/postimages/introduction-xamarin-forms-sharpuladora__grid.png" title="Grid" />
  
Once we have created the layout, it is important to tell our page to use it as a content, the way to do it is by specifying the property `Content` at the end of the page's constructor with the following line of code:  

```csharp  
Content = _layout;
```  

##### Code
File: `CalcPage.cs`

## Part 4 - The interface element  
Now that the container of our elements (the layout) has been created, it is the time to create the all the interface elements.  

We will use buttons for the digits and operators as well as a label to show the result.  

Initializing controls is pretty simple, for example, to create the button that will serve as the '0' in our calculator, we must write something like the following code:  
 
```csharp  
_b0 = new Button { Text = "0" };
```  

Once the control has been created we must add it to the containing layout to be shown in the screen. Remember that in this case the layout is made of rows and columns, so we must set a column number and column number where it must appear on the screen,
the `Grid` class allows us to set those values via the static `SetColumn` y `SetRow` methods. In the code below the first column and the fourth row are set to the button `_b0`:  
  
```csharp  
Grid.SetColumn(_b0, 1);
Grid.SetRow(_b0, 4);
```  

Next, we add it to the layout by using this line:  
  
```csharp  
_gridLayout.Children.Add(_b0);
```  

For the label where the result will be shown, we will use the `SetColumnSpan` methods to indicate that our control that it has to span across more than one column within the layout. In this case, we are setting ca column span of 4 to the `_resultDisplay` label:  
  
```csharp  
Grid.SetColumnSpan(_resultDisplay, 4);
```  

We also can use `SetRowSpan` if we want a control to use more than one row.

##### Code
File: `CalcPage.cs`

## Part 5 - The events  
In a calculator like the one we are doing, user interaction is required and in Xamarin.Forms such interactions are handled through events. Each of the controls we have added can have one or more events that are triggered by user interactions (or any other fragment of our code), we, as programmers, need to tell which events we want to handle and how are we going to handle them.  
  
For our calculator we will handle the `Clicked` event of the buttons. To handle events is a simple task in C#, all we have to do is to assign (using the `+=` operator) an event handler to the desired control. In the code below an event is specified as a handler for the `Clicked` event of `_b0`:
  
```csharp  
_b0.Clicked += OnNumericButtonClicked;
```  

The `OnNumericButtonClicked` handler is just a method with `void` as return value and gets passed in two arguments:    

 - `object sender` which is a reference to the control that triggered the event
 - `EventArgs e` which are some arguments related to the triggered event 

As an example, here is the implementation of the method for our calculator keyboard buttons. In the first line  a cast is made from `sender` to `Button`, and that is valid (and does not throw an exception) because as we said before: `sender` contains the control that triggered the event.
   
```csharp  
void OnNumericButtonClicked(object sender, EventArgs e)
{
	Button botonClickeado = (Button)sender;
	if (primerNumero == null || primerNumero == "")
	{
		primerNumero = botonClickeado.Text;
	}
	else
	{
		segundoNumero = botonClickeado.Text;
	}
}
```  

The handlers can be shared among several controls, so you do not have to create a handler for each control you use in your application. Now is your turn to build all the calculator's logic using the controls and the event handlers. If you get stuck, you can always take a look to the file indicated below to get you started.

##### Code
File: `CalcPage.cs`

## Part 6 - A small trick    
By this point you may think that our calculator is (almost) completed, but that is not the case. If you run it as is, you will notice that the calculator does not fill the entire screen... this is a small bug in our code. To work around this problem we will use another kind of layout that wraps our original layout. For this a `RelativeLayout` will come handy, this layout gives us a lot of flexibility when it comes to control how the interface elements are shown. As useful as this is, it requires more configuration. Go ahead and replace the `Content = _layout;` assignation to the following fragment of code:
  
```csharp  
// Trick to make our calculater fullscreen
var relativeLayout = new RelativeLayout();
relativeLayout.Children.Add(_layout, // <= Original layout
	Constraint.Constant(0),
	Constraint.Constant(0),
	Constraint.RelativeToParent(p => p.Width),
	Constraint.RelativeToParent(p => p.Height));
Content = relativeLayout;
```  

# What's next?
As you have realized, this application is not finished, we have to implement some handlers and all the calculator's logic. Your mission, should you choose to accept it, is to finish building the *Sharpuladora*. You can check the book from Charles Petzold <a href=" https://developer.xamarin.com/guides/cross-platform/xamarin-forms/creating-mobile-apps-xamarin-forms/" target="_blank">Xamarin.Forms Book Preview 2</a>.
