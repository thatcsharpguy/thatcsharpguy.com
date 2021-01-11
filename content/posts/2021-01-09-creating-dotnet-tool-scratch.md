---
layout: post
lannguage: en
title: Creating a dotnet tool (part 1)
date: 2021-01-09 01:00:00
author: Antonio Feregrino
summary: In this posts series I'll show you how to create a dotnet tool from scratch, following the best software engineering practices such as version control, continuous integration and thorough testing.
image: https://i.imgur.com/qUPBAyW.png
tags: software-engineering, testing, dotnet, c-sharp
---

If you speak spanish and prefer to see me coding this, you can do so in this video: [Ingeniería de software  - Comenzando con nuestro proyecto](https://youtu.be/wXseAL7NCnk).

Lockdown is a static website generator written in C# that works as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools).

Through this series of posts I'll guide you on how did I build this tool with the hopes that you will be able to create another *dotnet* tool (hopefully you won't build a better Lockdown competitor, though 😅).

## Tooling  

Throughout this journey I'll be using mainly three tools:  

 - Visual Studio or Visual Studio Code
 - Console (whether is on Windows, Linux or Mac)

## Setting up a Git repo  

Obviously, we'll need a Git repository to take care of our Version Control necessities while building our tool.

I'll use a pre-created `.gitignore` file from [gitignore.io](https://www.toptal.com/developers/gitignore/api/dotnetcore,visualstudiocode,visualstudio).

Let's also add a `readme.md` and our `license` file, to start everything right. Make sure you choose the right license for your project!

## Adding our projects 

### Main project

Though you can create the projects by using the GUI, I'll strongly recommend you get used to interact with the `dotnet` command from the console, and to get started this is what we are going to do to create our main project:  

```bash
dotnet new console -n Lockdown
``` 

### Test project

Do not forget a test project! since we expect our tool to be a long term project, having tests is an essential part of the process. In this case, we will be using *xUnit*-based tests, though there are some alternatives like *NUnit* or *MSTest*.

```bash
dotnet new xunit -n Lockdown.Test
``` 

### Putting everything together  

We need to add a reference from our main project to our test project:  

```bash
dotnet add Lockdown.Test/Lockdown.Test.csproj reference Lockdown/Lockdown.csproj 
``` 

Lastly, a .NET project wouldn't be a .NET project without a solution file, let's add one and then add our projects into it:  

```bash
dotnet new sln -n Lockdown
dotnet sln add \
    Lockdown.Test/Lockdown.Test.csproj \
    Lockdown/Lockdown.csproj
``` 

Now we can simply call `dotnet build` and have all our projects compile, or we can call `dotnet test` and have all our tests run.

## Coding with style 😎  

Since we want to set up everything properly from the beginning, we need to add to enforce a style guide, we do not want our code to be messy, right? We will be using the package `StyleCop.Analyzers`. Now, for the moment I want to enforce the style guide in the actual production code so we need to add it to that project alone:

```bash
dotnet add Lockdown/Lockdown.csproj package StyleCop.Analyzers
```

Now, if you try to build the project you will see some warnings, maybe a lot of them. Currently you do not have control over what warnings lie within your code, to change that it is necessary to create something called a ruleset, you can use [this template](https://github.com/DotNetAnalyzers/StyleCopAnalyzers/blob/master/StyleCop.Analyzers/StyleCop.Analyzers.ruleset) to guide you in the creation of your file.  

Let's say, we create our own version in the file `Style.ruleset`.

The next task would be to tell our project to use this newly created ruleset. To do this, it is necessary to modify our *csproj*:  

```xml
<PropertyGroup>
    <CodeAnalysisRuleSet>../Stlye.ruleset</CodeAnalysisRuleSet>
</PropertyGroup>
```

Now we can simply edit our ruleset to fail when some "major offence" happens or just ignore some rule we do not care about.

## Configuring a CI pipeline!  

Of course, having tests and a style guide are just part of the trick, what is left is have those two things executed for us automatically, and in the cloud! We can use GitHub Actions to do that. 

To start using GitHub Actions what you need to do is create a file called `ci.yml` (the file can be named anything as long as it is a `.yml` file)

Without going into details, what we need to add into this file is the following:  

```yml
name: My first CI pipeline

on: [push, pull_request]

jobs:
    main:
        runs-on: ubuntu-latest
        steps:

            - name: Checkout code
              uses: actions/checkout@v1

            - name: Set Up .NET
              uses: actions/setup-dotnet@v1
              with:
                dotnet-version: '5.0.x' # SDK Version to use;

            - name: Test
              run: |
                dotnet test

            - name: Build
              run: |
                dotnet build
```