---
layout: post
title: Xamarin.Android vs traditional Android
date: 2015-11-16 21:00:00
author: Antonio Feregrino
featured_image: featured.jpg
categories: xamarin
lang: en
summary: There are no significant differences between traditional Android development and Xamarin.Android, meaning that if you have previous knowledge you can apply it without concerns but instead of writing Java you will be writing awesome C# code that can potentially be shared along any other platform.
github: https://github.com/fferegrino/Xevenshtein
tweet_id: 667182022071345152
tags: VisualStudio, Xamarin
featured_tag: Xamarin
---

I have to be honest. It has been a while since I did an Android app but today I tried to remember my little knowledge to compare traditional Android Development and Xamarin.Android. To create this post I used Android Studio 1.1.0 y Xamarin Studio 5.7.2. 

Even though the differences are very subtle, and the greatest one of everyone lies in the code, they do exist. I'll start with the project structure:

### Project structure
<img src="https://thatcsharpguy.github.io/postimages/xamarin-android-vs-traditional/comparison.png" title="Comparison project structure" />

<ol>
	<li><b>Activities</b> (and other UI related code stuff). As you might know in Java exist the concept of <code>Packages</code>, and it is strongly asociated to the underlying folder structure. In Android Studio the class <code>MainActivity.java</code> is in the package <code>org.fferegrino.android.xevenshtein</code> whereas in Xamarin Studio the class `MainActivity.cs` is directly in the Solution's root.</li>
   	<li><b>Application logic</b> following the concept of <a href="https://xsa.ghost.io/code-organization-xs-vs/" target="_blank">Solution</a>, when using C# we could easily place our application logic in another project, meanwhile, using Java the alternative is to create another package</li>
  	<li><b>Manifest</b> the manifest is the same, except in when declaring the entrey point to our application, in traditional Android it is done in this file, in Xamarin.Android it is declared elsewhere.</li>
   	<li><b>Resources folder</b> as the Manifest, the resources folder is the same with some minor changes in the naming conventions (uppercasing, full file names...), and as we will see later, the files itself are the same.</li>
</ol>

### Resource files
Layout files are the same in both approaches, in fact, I copied the content of `Main.axml` (Xamarin) to `layout_main.xml`and the result was the same. This worked too for string resources named `String.xml` and `string.xml`.

### The code
In Xamarin.Android our classes related to the UI have to derive from Activity as in traditional Android, here is the **class declaration**:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android-vs-traditional/class-declaration-cmp.jpg" title="Class declaration" />

In the C# version you can see the `Activity` decorator, which tells the name of the activity (`Label`), wether it is the app launcher(`MainLauncher`) and the icon (`Icon`), these stuff goes in the Android Manifest in the traditional approach.

For **UI elements** such as Buttons, TextViews and EditTexts are equally named and declared:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android-vs-traditional/field-declaration-cmp.jpg" title="Fields declaration" />

By convention, in C# methods must start with an uppercase letter, and the override indicator is not an annotation but a keyword, this is the `OnCreate` **method**:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android-vs-traditional/method-declaration-cmp-1.jpg" title="On create method" />

Another interesting thing is the automatically generated `R` or `Resource` class, when using Xamarin this class is named `Resoruce`, and we can use it to **find views**:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android-vs-traditional/view-finding-cmp.jpg" title="FindViewById" />

As you can see, the diamond syntax provides a way to return the view without casting the view ourselves. 

I left the best to the end. There are several ways to implement an **event handler** but C# has one of the coolest (using a Lambda expression):

<img src="https://thatcsharpguy.github.io/postimages/xamarin-android-vs-traditional/event-handling-cmp.jpg" title="Event handler" />

By the way, if you are interested in the code I used to make this post, check it on [GitHub](https://github.com/fferegrino/Xevenshtein) or download it [here](https://github.com/fferegrino/Xevenshtein/archive/b-p-2.zip): 

### tl;dr
There are no significant differences between traditional Android development and Xamarin.Android, meaning that if you have previous knowledge you can apply it without concerns but instead of writing Java you will be writing awesome C# code that can potentially be shared along any other platform.