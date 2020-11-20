---
layout: post
title: Xamarin.Forms 2 and UWP
date: 2015-12-06 21:00:00
author: Antonio Feregrino
summary: Xamarin 4 has everything you need to create great mobile apps. Check out Universal Windows Platform Apps, my favorite feature.
lang: en
featured_image: featured.png
github: https://github.com/fferegrino/Xevenshtein-UWP
tweet_id: 673499977281241088
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

You have heard me talk about Xamarin.Forms before and you know that I'm all mobile... but what you might have missed is the fact that I'm a HUGE Windows Platform fan. This is why this new release of Xamarin has got me excited. In this release they included <a href="http://developer.xamarin.com/guides/cross-platform/xamarin-forms/windows/getting-started/universal" target="_blank">support for Universal Windows Platform Apps</a> which, even though is still a preview version, it is a great tool for us as developers to start bringing our apps to even more devices.  

For this post I'll be using a very simple app that I created in the past when I wrote about [going from console to mobile](/post/from-console-to-mobile), however, this time is all about "from mobile to Windows Platform".  
 
 
## How to...  
We'll be following the official documentation, so fasten your seatbelt and get ready to learn how is Xamarin.Forms wired and called within a platform specific project.  

### Infrastructure
Feel free to download the initial source code from <a href="https://github.com/fferegrino/Xevenshtein-UWP/releases/tag/uwp-post-1" target="_blank">here</a>, extract it somewhere and open the `Xevenshtein.sln` file. Once the solution is open, upgrade all Xamarin.Forms packages to any version 2 (Xamarin.Forms 2.0 is part of Xamarin 4 release).  

<img src="/images/xamarin-forms-uwp__xamarin-forms-2.png" title=""Xamarin.Forms in the package manager"" />

Then add a new project, this will be our UWP app and add the Xamarin.Forms NuGet package to it  

<img src="/images/xamarin-forms-uwp__add-uwp.png" title=""Adding an UWP app"" />
<img src="/images/xamarin-forms-uwp__add-nuget.png" title=""Adding the NuGet"" />

Add a reference to the project where the forms application is, for us, such project is named `Xevenshtein` and is a PCL  

<img src="/images/xamarin-forms-uwp__add-reference.png" title=""Adding a reference to the Forms app"" />

### Code

It is time to get our hands into the code: Open the `App.xaml.cs` file that was created with the UWP app, look for this handler assignation `rootFrame.NavigationFailed += OnNavigationFailed;`. Below this line we must initialize the Xamarin.Forms module (every platform specific project that uses Xamarin.Forms has to do this as one of the first things in the app lifecycle), to do so, simply call the `Init` method:  

```csharp  
rootFrame = new Frame();

rootFrame.NavigationFailed += OnNavigationFailed;

Xamarin.Forms.Forms.Init(e); // Added support for Forms
```  
<br />
Then, we must get rid of the default UI inside the  `MainPage.xaml`, we won't be using it since Forms has got us covered. Delete all code (it might be just a grid) within the `<Page>` and `</Page>` tags.  

<img src="/images/xamarin-forms-uwp__grid-removal.png" title=""Removing useless UI"" />

Then, we need to change our page type to a special kind of Xamarin.Forms page. First add this *"attribute"* inside the `<Page>` tag:

```xml  
xmlns:forms="using:Xamarin.Forms.Platform.UWP" 
```  
I double-quoted *attribute* since the line above isn't exactly an *attribute*, it is a *namespace*, somewhat like an `using` directive but for xaml. Once we have added the namespace, replace the page `<Page ` and `</Page>` for `<forms:WindowsPage ` and `</forms:WindowsPage>`, as you can see we're using the `forms` namespace.

<img src="/images/xamarin-forms-uwp__final-result.png" title=""Final result"" />
    
<br />
And that's it for the XAML part, press `F7` to jump to the code-behind.  

First things first, since our MainPage isn't a `Page` but a `WindowsPage`, replace the inheritance `: Page` for `: WindowsPage`  

```csharp  
public sealed partial class MainPage : WindowsPage  // bye, bye ": Page"
```  

Even though replacing the inheritance in this page isn't needed (because the other partial definition of the class has it), I always do it, it gives me clarity of what type the page is.  

<br />
Next... the big step: load your Forms page, this is done inside the `MainPage` constructor and after the elements of the page have been initialized:  

```csharp  
this.InitializeComponent();

LoadApplication(new Xevenshtein.App()); // Call to Xamarin.Forms 
```  

And that's it, now you have a nice UWP app in a few steps. 
<br />

## What's next?  
For you: keep playing around with the Xevenshtein app (<a href="https://github.com/fferegrino/Xevenshtein-UWP" target="_blank">the code is on GitHub</a>), or go ahead and create an UWP version your Forms apps: just imagine one of your apps running on an Xbox One or a giant Surface Hub... but remember that this isn't the final release so if you find a bug, be a good Xamarin citizen and <a href="https://bugzilla.xamarin.com/enter_bug.cgi?alias=&assigned_to=&attachurl=&blocked=&bug_file_loc=http%3A%2F%2F&bug_severity=normal&bug_status=NEW&cf_tags=&comment=&contenttypeentry=&contenttypemethod=autodetect&contenttypeselection=text%2Fplain&data=&deadline=&dependson=&description=&estimated_time=&form_name=enter_bug&maketemplate=Remember%20values%20as%20bookmarkable%20template&op_sys=Mac%20OS&product=Forms&rep_platform=PC&short_desc=&target_milestone=UWP&version=1.5.1">report it here</a>. Oh, and don't forget to take a look to other Xamarin 4 features, like <a href="http://rasmustc.com/blog/compiled-xaml-views/" target="_blank">xaml compilation</a> or the general availability of <a href="http://pumpingco.de/building-better-apps-with-xamarin-insights/" target="_blank">Xamarin Insights</a>. 

For me: I'll be using UWP to create a simple markdown viewer, I'll post about it later on.

