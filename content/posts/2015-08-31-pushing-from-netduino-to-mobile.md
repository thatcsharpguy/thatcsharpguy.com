layout: post
title: Pushing with Parse from Netduino to Xamarin.Forms
date: 2015-08-31 21:00:00
author: Antonio Feregrino
featured_image: featured.jpg
categories: xamarin netduino parse parse.com iot sockets xamarin.forms poc xamarin
excerpt: It's been a while since I took my Netduino out of its box and I did something with it, back then I promised to do a slightly more complex system using a push notification service.
alias: /pushing-from-netduino-to-mobile/index.html
lang: en
tags: Xamarin, Netduino
featured_tag: Xamarin

It's been a while since I took my Netduino out of its box and I did something with it, back then I promised to do a slightly more complex system using a push notification service. I quote my own words: *"I'll create a system that sends a notification over to my phone everytime someone opens my bedroom door"*. Well, here it is (and it's called Doorduino <small>such an awful name, I know</small>).

### On Parse  
Parse is a BaaS (Backend as a service) providers that amongst its set of features, offers a Push Notifications service. I will be using that service to send the push notifications to my devices. It was pretty straight forward to get started, first of all, I created a new app, grabbed the keys (for both REST API and .NET), wrote some code and start sending pushes.

### Setting up the Netduino  
I used a magnetic contact switch to detect when the door as been opened or closed, in the code you'll se something like `var doorPort = new InputPort(...` and an infinite `while` loop where the "main" logic resides. Within the `while` the netduino reads the current state of the sensor, compares it with a previous state and if there are any changes sends the push notification using my `parse-dotnetmf` library.  

#### The parse-dotnetmf library  

To consume Parse's REST Api features from a Netduino 3 I decided to create a client library to make things easier for me and possibly you, this library currently only supports sending push notifications to Parse, but can easily be extended to support all other features as well, it is easy to use just create a client `var pc = new ParseClient("AppId", "Api Key");` and then send a push notification like this: `pc.SendPushToChannel("Hey!");`.  

This library is strongly inspired in the MicroTweet2 library by Matt Isenhower. It uses the `HttpWebRequest` class, does not require additional dependencies and it easily fits on a Netduino 3 Wi-Fi board.

### Setting up the Xamarin.Forms app  
There are some steps that you must follow to implement Parse Push into your Xamarin App, I recommend you to follow the tutorials that already exist on Parse's website. I'll make a quick recap of what I did (besides trying to create my own plugin).  
<br />
First of all, for Windows Phone there are no required steps other than following <a href="https://www.parse.com/apps/quickstart#parse_push/windows_phone/existing" target="_blank">this tutorial</a>, on the other hand, for Android you will have to do two things:  
 1. Check this <a href="https://parse.com/apps/quickstart#parse_push/android/native/existing" target="_blank">tutorial</a> but do only the `AndroidManifest.xml` part.  
 2. Check the <a href="https://github.com/ParsePlatform/PushTutorial/tree/master/Xamarin" target="_blank">sample on GitHub</a>, particulary the implementation of the `ParseApplication` class in the Android project.  
For iOS, well this is a bit of a turn down, you must discover it by yourself since I have not implemented Doorduino for that platform. I suggest that you follow the steps in the tutorial and I’m here to help if you need it, though.

#### The CrossParse plugin  
As we know, when we want to deliver platform specific experiences from a Xamarin.Forms app we can use or create a plugin that will let us abstract that functionalities away from each platform to interact with each platform via a common API. I tried to do a plugin to handle all interactions with Parse push but turns out that this isn’t as easy as I thought at first. I mean the Parse’s Xamarin Components for each platform already support push notifications but there is no abstraction layer that can be used with Forms, not to mention that there are some required special steps for each platform that you must manage explicitly in each platform specific project (such as creating the extra `ParseApplication` class in the Android project, for example).  

So this plugin isn't as useful as I wanted it to be at first but it still helps by providing the mentioned abstraction layer and installing Parse's nugets for you. With this plugin you can subscribe/unsubscribe to channels and handling notifications while the user is in the app. I really hope Parse release a plugin for Forms soon as it is fairly easy to do given they have already have the components.  

### It is alive!
I did a video where I show how the system works:  
<iframe width="560" height="315" src="https://www.youtube.com/embed/0nbhVD7Ox00" frameborder="0" allowfullscreen></iframe>

### Links  
I didn't wanted to create a post filled with links so here are the links to my plugins GitHub repos (and their respective NuGet page):  
 - parse-dotnetmf [(GitHub repo)](https://github.com/fferegrino/parse-dotnetmf) and [NuGet](https://www.nuget.org/packages/M16.Parse.DotNetMf/)  
 - CrossParse plugin [(GitHub repo)](https://github.com/messier16/xamarin-plugins/tree/master/M16.Parse) and [NuGet](https://www.nuget.org/packages/Xam.Plugin.Parse/)  
 - Doorduino [(GitHub repo)](https://github.com/fferegrino/doorduino) holds both Netduino and Xamarin.Forms apps.  

## What's next?
I just got a nice 37-in-1 sensor box from China so hopefully this kind of posts won't stop. Anyway, feel free to reach out letting me your dubts or what topics do you want me to blog about, below is my contact information. Remember, I'm here to help :)