---
layout: post
title: How is my code organized in Visual Studio?
date: 2015-11-16 21:00:00
author: Antonio Feregrino
featured_image: featured.jpg
categories: xamarin
tweet_id: 667177573508608000
lang: en
excerpt: If you have ever used an IDE to develop any application you might have wondered how do these kind of programs know where are the components for each one of the things that make your app compile or run. In this post I'll try to explain how Xamarin Studio and Visual Studio keeps that control.
tags: VisualStudio, Xamarin
featured_tag: VisualStudio
---

If you have ever used an IDE to develop any application you might have wondered how do these kind of programs know where are the components for each one of the things that make your app compile or run. In this post I'll try to explain how Xamarin Studio and Visual Studio keeps that control.

### The Solution
Both Visual Studio and Xamarin Studio organize our code, assets, libraries and other stuff in a structure called *Solution*, all this information is stored within a file with extension **.sln**. The **.sln** file is an (usually) automatically generated plain text file that tells the IDE where to find the projects related to our application.

<img src="/images/code-organization-visual-studio__solution-in-folder.png" title=""Image of a solution file"" />

This is how a Solution (the free C#/F# shirt app solution) looks like when opened in Xamarin Studio and Visual Studio:

<img src="/images/code-organization-visual-studio__sln-opened.png" title=""Image of opened solutions"" />

And this is how a the same file looks like when opened in a plain text editor.

<img src="/images/code-organization-visual-studio__sln-plain-text-explained-2.png" title=""Image of opened solutions"" />

<dl>
<dt>1. Solution</dt>
<dd>The file itself, the name we see in the IDE is the same name of the file plus the <code>.sln</code> extension.</dd>
<dt>2. Solution folder</dt>
<dd>These are logical folders that we can use to organize our solution.</dd>
<dt>3. Shared project</dt>
<dd>A shared project (we'll talk about them on a later post).</dd>
<dt>4. C# project</dt>
<dd>A C# project, in this case refers to an Android project, in Xamarin is a convention to use the <code>.Droid</code> suffix to denote that.</dd>
<dt>5. C# project</dt>
<dd>Another C# project, in this case refers to an iOS project, in Xamarin is a convention to use the <code>.iOS</code> suffix to denote that.</dd>
</dl>


### The .[something]proj file

As I mentioned above, the Solution keeps track of the projects that comprise our application, but what is exactly a project?

Well, a Project is a logical way to organize a project's files (classes, resources, images...) it is described in a file ending with `proj`, being `.csproj` for C# projects, `.shproj` for shared, other kind of projects end in `.vbproj`, `.jsproj`, etc.

As you can see in the previous image, the `.sln` file contains "links" to these projects.

These are the projects for the Free Xamarin Shirt App:

<img src="/images/code-organization-visual-studio__freeshirtappproject.png" title=""Xamarin Free Shirt App"" />

### tl;dr
We can explain how is our code organized with a simple diagram. 

<img src="/images/code-organization-visual-studio__Solution-diagram.jpg" title=""Diagram of how solution works"" />
