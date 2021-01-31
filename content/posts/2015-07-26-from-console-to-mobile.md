---
layout: post
title: From console to mobile
date: 2015-07-26 18:00:00
categories: xamarin
summary: What happens when we face real people/world problems? for sure, there are still console apps out in the wild, but nowadays all the successful applications have a mobile version, why yours shouldn't have one?
lang: en
featured_image: featured.png
alias: /from-console-to-mobile/index.html
github: https://github.com/fferegrino/Xevenshtein
tags: Xamarin
featured_tag: Xamairn
---

At school I was often asked to write small console apps to practice programming concepts, and some teachers even wanted a fully functional project in such way.  
<br />
<p>But what happens when we face real people/world problems? for sure, there are still console apps out in the wild, but nowadays all the successful applications have a mobile version, why yours shouldn't have one? In this post I'll try to show you how to go from console to mobile (using Xamarin.Forms) with a simple example, it's a bit long but I'll make it worth the reading.</p>

<h3>Console</h3>
<p>I'd like to start by introducing the app that we'll transform to mobile, this app takes two strings and returns a number indicating the "distance" between them using the <a href="http://www.dotnetperls.com/levenshtein" target="_blank">Levenshtein distance algorithm</a>, for example:</p>
<table>
	<thead>
		<tr>
			<td>str1</td>
			<td>str2</td>
			<td>Distance</td>	
		</tr>
	</thead>
	<tr>
		<td>ant</td>
		<td>aunt</td>
		<td>1</td>
	</tr>
	<tr>
		<td>Samantha</td>
		<td>Sam</td>
		<td>5</td>
	</tr>
	<tr>
		<td>Xamarin</td>
		<td>Samaryn</td>
		<td>2</td>
	</tr>
</table>
<p>I'll use the implementation from Dot Net Perls, which is a nice static method that we can easily call in our program. Now, in a console app we ask the user for input with <code>Console.ReadLine()</code> in this case we'd use something like this:</p> 

```csharp  
static void Main()
{
	Console.WriteLine("First string:");
	string str1 = Console.ReadLine();
	Console.WriteLine("Second string:");
	string str2 = Console.ReadLine();
	int distance = Compute(str1, str2);
	Console.WriteLine("Distance: " + distance);
}
```  

<img src="https://thatcsharpguy.github.io/postimages/from-console-to-mobile/sample-c-app.gif" title="Sample app" />

<h3>Some differences</h3>
<h4>Entry point</h4>
<p>As we have seen before, our program has an entry point (the <code>Main</code> method) whereas for mobile every platform has its own entry point, not necessarily a Main method. Other important thing is that  for a console app we tend to write most of the application logic inside the entry method, while the entry method for a mobile application runs code that usually isn't user provided code.</p>
<h4>Controls</h4>
<p>Something that users are looking for is having a nice UI to interact with the app, in the console user must interact sequentially, in our example a user that has entered the first string cannot go back to modify it. Or think about what happens if for some reason the user wants to type the second string before the first one? In a mobile app we can solve this problem by using controls, with them we can forget about Console writing or reading.</p>
<br />
<p>A control is one of the many ways to receive user interaction or to provide feedback to the user about what is happening. In our example we will replace both <code>Console.ReadLine</code> for a pair of <code>Entry</code> controls and the <code>Console.WriteLine</code>s for <code>Label</code>s. The controls are available for user interaction at any time.</p>
<h4>Event oriented programming</h4>
<p>There are few user interactions that we can handle when programming a console applications, the user can only enter text, use the keyboard arrows or press the enter key. On the other hand if we use controls in our mobile app a whole lot of user interactions can be made. When a user interacts with a control an event is fired, we as programmers provide code to handle such events. We call that "event oriented programming" since the behaviour of our app is driven by the events we choose to handle.</p>
<h3>Xamarin.Forms</h3>
<p>So, what is Xamarin.Forms? in a few words it is a tool provided by the Xamarin Platforms that allows us to share even more code when writing an app. Xamarin.Forms (I'll be calling it just "Forms" from now on) provides a set of common controls to write our application interface once and have it work on the three major mobile platforms. The controls we create using Forms are rendered to platform specific controls in order to take advantage of each operating system capabilities. For example: if we develop using the traditional approach we must work on the UI three times (each for every operating system).</p>

<p>As I mentioned before, the Levenshtein app will have two text input boxes, three labels and one button. If we had chosen the traditional approach we'll had to write six text input boxes (two for each platform), three buttons (again, one for each platform). Look at the table below to see what the controls we'll be using render to:</p>
<table>
	<thead>
		<tr>
			<td>Forms</td>
			<td>Android</td>
			<td>iOS</td>	
			<td>Windows Phone</td>	
		</tr>
	</thead>
	<tr>
		<td>Button</td>
		<td>Button (View)</td>
		<td>UIButton (UIControl)</td>	
		<td>Button (Control)</td>	
	</tr>
	<tr>
		<td>Label</td>
		<td>TextView (View)</td>
		<td>UILabel (UIControl)</td>	
		<td>TextBlock (Control)</td>	
	</tr>
	<tr>
		<td>Entry</td>
		<td>EditText (View)</td>
		<td>UITextField (UIControl)</td>	
		<td>TextBox (Control)</td>	
	</tr>
</table>
<h3>Hands on code</h3>
<p>Since I want to show all three platforms I'll be using Visual Studio 2013 to create the project but this could be easily replicated using Xamarin Studio if you only want to deploy to certain platform.</p>
<h4>Creating the project</h4>
<p>To create the project we'll use the Mobile Apps template that comes with Xamarin Platform installation. To do this once Visual is open go to <code>File &gt; New &gt; Project</code> and select Mobile Apps and then Blank App (the one that says "portable"). Assign your project a name and let VS do its magic. It shouldn't take long, after it finishes we'll end up with a structure like the one below.</p>

<img src="https://thatcsharpguy.github.io/postimages/from-console-to-mobile/project-structure.png" title="Project structure" />

<p>You can see that there are 4 projects, three of them ending with a suffix indicating the platform they are specific to. The other one is a portable class library (from now on "PCL") which will contain most of our code. You also must notice that the name of the PCL is highlighted with a bold font, it means that the PCL is set as the <strong>startup project</strong>.</p>
<h4>Running the startup project</h4>
<p>The startup project is the project that will get executed once we click the "Play" button o the upper part of our IDE, we can change the startup project by right-clicking on the project we want to set and choosing the corresponding option.</p>
<p>Depending on what kind of project have we selected as startup, a different set of options will be available to run our project, let's keep it simple and set the Windows Phone project as the startup one. Set the emulator as the target device and hit Play to build and run the app.</p>

<img src="https://thatcsharpguy.github.io/postimages/from-console-to-mobile/project-target-selection.png" title="Startup project" />

<h4>What's inside a Xamarin.Forms app</h4>
<p>Let's go back to the PCL project, in it we'll find a file named <code>App.cs</code> it contains the <code>App</code> class which we can see as the "entry point" for our Forms app, I quoted entry point because it isn't true: as I said before, every platform has its own entry point. But for now we'll see the App.cs file as if it were.</p>
<p>The App class has a constructor where we must create the content that the user will see on its device once our app has finished loading. This time we'll be using C# to create the interface but it is easy to switch from a code-based interface to a XAML-based one.</p>
<h4>Adding controls</h4>
Look at this: 

```csharp  
// Class constructor
public App()
{
    // The root page of your application
    MainPage = new ContentPage
    {
        Content = new StackLayout
        {
            VerticalOptions = LayoutOptions.Center,
            Children = {
				new Label {
					XAlign = TextAlignment.Center,
					Text = "Welcome to Xamarin Forms!"
				}
			}
        }
    };
}
```  

<p>It is easy to understand what is happening over there, <code>MainPage</code> tells the app that a <code>ContentPage</code> should be the first thing te user see. At the same time, that <code>ContentPage</code> has a <code>StackLayout</code> (as its name suggest, whatever we put in that layout will appear stacked in the order we add them to it).  The <code>Children</code> property is initialized jsut to a <code>Label</code> with an alignment rpoperty and a <code>Text</code> property set to the string "Welcome to Xamarin Forms!". We can start from here to buid our app. Erase everything inside the <code>App</code> constructor we'll write something useful:</p>

<p>Start by creating a new <code>ContentPage</code>, and name it <code>xevenshteinMainPage</code>, and a new <code>StackLayout</code> and name it <code>mainLayout</code>.</p>

```csharp  
public App()
{
	ContentPage xevenshteinMainPage = new ContentPage();
	StackLayout mainLayout = new StackLayout();
```  

<p>Next let's create some controls, we'll create two <code>Entry</code>, three <code>Label</code> and a <code>Button</code>, we'll declare the controls outside the <code>App</code> constructor since we'll need them outside the constructor. Always try to give your controls and variables meaningful names. Add this code above the class constructor:</p>

```csharp  
Button computeButton;
Label firstStringLabel, secondStringLabel, computationResultLabel;
Entry firstStringEntry, secondStringEntry;

// Class constructor
public App()
{
```  

<p>By this point we have done a lot, but our controls aren't created yet, the following code will do so. Notice that some controls are created using property initializing, it allows us to set some properties at the time of instantiation instead of doing it later. The following code is supposed to be after the creation of the <code>mainLayout</code>.</p>

```csharp  
	// ... Above is the instatiation of mainLayout
	
    computeButton = new Button { Text = "Compute" };
    firstStringLabel = new Label { Text = "First string" };
    secondStringLabel = new Label { Text = "Second string" };
    computationResultLabel = new Label { Text = "No distance calculated yet" };
    firstStringEntry = new Entry();
    secondStringEntry = new Entry();
```  

<p>Our controls are created now! but they don't appear on the page if we run the app. That is because we haven't told our code to show them. In the following code we tell the code to add them to the view, see how we are using the <code>Children</code> property of the <code>mainLayout</code> to add the controls in the order we want them to appear, later, we set the <code>mainLayout</code> as the content of the <code>xevenshteinMainPage</code> and a line below we also set the  <code>xevenshteinMainPage</code> as the <code>MainPage</code> for our app.</p>

```csharp  
	// ... Above is the instatiation of secondStringEntry
	
	mainLayout.Children.Add(firstStringLabel);
    mainLayout.Children.Add(firstStringEntry);
    mainLayout.Children.Add(secondStringLabel);
    mainLayout.Children.Add(secondStringEntry);
    mainLayout.Children.Add(computeButton);
    mainLayout.Children.Add(computationResultLabel);

    xevenshteinMainPage.Content = mainLayout;
    MainPage = xevenshteinMainPage;
```  

<img src="http://i.giphy.com/l41lLKZiUBR2tK2By.gif" alt="App shows controls but no behavior" />

<p>By this point the controls we've created appear when running the app, when we interact with them some events get fired, it is time for us to handle an event and add behaviour to the app. In particular, we want to know when the user presses the "Compute" button, thus we must add an event handler to the click event, inside the event handler we will perform the calculation and show the result on the screen. We do so by creating a method that receives two parameters (<code>object</code> and <code>EventArgs</code>), after that using the <code>+=</code> operator we add it to the <code>Clicked</code> check out the following piece of code.</p>

```csharp  
//
	computeButton.Clicked += ComputeButton_Clicked;
}

void ComputeButton_Clicked (object sender, EventArgs e)
{
	string str1 = firstStringEntry.Text;
	string str2 = secondStringEntry.Text;
	int distance = Xevenshtein.Algorithm.LevenshteinDistance.Compute(str1, str2);
	computationResultLabel.Text = "Distance: " + distance;
}
```  

<img src="http://i.giphy.com/3o85xmAGsOX5wlZX7W.gif" alt="App shows controls but no behavior" />

<p>Run the project and that's it! you have now created your first Xamarin.Forms app, if you ever need help with any of the steps above please let me know. I have uploaded all the code to <a href="http://github.com/fferegrino/Xevenshtein">GitHub</a> so you can check my working solution. From here you now may want to <a href="http://developer.xamarin.com/guides/cross-platform/xamarin-forms/working-with/styles/" target="_blank">style your app</a> or dig a bit more into <a href="http://developer.xamarin.com/guides/cross-platform/xamarin-forms/working-with/app-lifecycle/" target="_blank">app lifecycle</a>.</p>