---
layout: post
lannguage: en
title: Creating a dotnet tool (part 3)
date: 2021-01-23 01:00:00
author: Antonio Feregrino
summary: This time I'll show you how to perform Continuous Deployment to NuGet from GitHub Actions.
image: https://i.imgur.com/C0Nqt9A.png
tags: software-engineering, testing, dotnet, c-sharp
---

 > This is a rough draft: explanations are incomplete and there may be spelling mistakes. I just made it public for people that have a hard time copying commands from a video.

If you speak spanish and prefer to see me coding this, you can do so in this video: [Ingeniería de software  - Comenzando con nuestro proyecto](https://youtu.be/wXseAL7NCnk).

Lockdown is a static website generator written in C# that works as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools).

Through this series of posts I'll guide you on how did I build this tool with the hopes that you will be able to create another *dotnet* tool (hopefully you won't build a better Lockdown competitor, though 😅).

## Topics

 - Adding versioning to our app
 - Releasing from CI
 - Working with the FileSystem
 - Tests against the file system

## Adding versioning to our app  

Let's install [versionbump](https://github.com/lockdownblog/bumpversion), which is, funnlily enough another `dotnet tool`:

 >   First of all, full disclaimer, `versionbump` is a tool developed by me and while it works, it may not be perfectly suited to your needs. You may consider using some other tools. Once you are advised.

```bash
dotnet tool install --global BumpVersion
```

Once you have done this, it may be wise to check the installation was smooth:

```bash
bumpversion --version
```

### Add configuration file   

Let's add a `.bumpversion.cfg` file to the root of our project:  

```text
[bumpversion]
current_version = "0.0.0"
commit = true
tag = true
```

Since we put our version number in code, we should also add the following section to our file:

```text
[bumpversion.file.0]
file = "Lockdown/Program.cs"
search = "{current_version}"
replace = "{new_version}"
```

Next up, is testing our tool is working by simply bumping the *patch* part with the following command:

```bash
bumpversion patch
```

Check your GitHub history, you'll see that there are new tags. These tags are living locally in our machine, but we need them on the server too, to push them it is necessary to do a 

```bash
git push --follow-tags
```

If you check GitHub, you'll see that now we have pushed our tags there also; from these tags you can create a new fancy release in your project's page telling the world about your new fancypants application.

## Integrating tags with our CI  

Next up, we need to make sure our CI pipeline treats our new tag in a special manner, to do this, let's create another CI workflow, this time, we'll call it `tags.yml`, and, for now, it will be almost a complete copy of our previous pipeline, the only bit that we'll change are the conditions under which this pipeline should be executed:

```yaml
name: Tags pipeline

on:
  push:
    tags:
      - 'v*'
```

This is telling the pipeline that it should run only when new tags are pushed to the repository, in this case, you can see, our tags should start with the "v" character.

## Preparing our NuGet package  

We are almost ready to publish a beta version of our package, and reserve our name in NuGet!, but first let's change our code to tell our users that this is not production ready:

```csharp
this.console.WriteLine("You executed the build command. This is a placeholder package for my new tool, coming soon");
```

Obviously, we also need to modify the tests:

```csharp
writtenText.ShouldBe("You executed the build command. This is a placeholder package for my new tool, coming soon." + Environment.NewLine);
```

### NuGet!

Once we've done this, head over to nuget.org and log in with a Microsoft Account, check that the name you'll use for your package is not taken. 

We need to add some metadata to our package in order for it to look nice and be indexable in the package directory. The file we need to modify is `Lockdown/Lockdown.csproj`.

To begin, we need to tell NuGet that our code is a *dotnet tool*:

```xml
<PackAsTool>true</PackAsTool>
<ToolCommandName>lockdown-test</ToolCommandName>
<PackageOutputPath>./nupkg</PackageOutputPath>
```

Then, let's set up some unique id versioning for our app:

```xml
<PackageId>Lockdown.Tutorial</PackageId>
<AssemblyVersion>0.0.1</AssemblyVersion>
<Version>0.0.1</Version>
```

Lastly, let's add even more metadata:

```xml
<Authors>fferegrino</Authors>
<Title>lockdown test</Title>
<PackageDescription>A static website generator</PackageDescription>
<Description>A static website generator</Description>
<PackageLicenseExpression>MIT</PackageLicenseExpression>
<PackageProjectUrl>https://github.com/lockdownblog/lockdown-en-vivo/</PackageProjectUrl>
<RepositoryUrl>https://github.com/lockdownblog/lockdown-en-vivo/</RepositoryUrl>
<PackageIcon>icon.png</PackageIcon>
<PackageTags>static;static-website;website;website-generator;html;jekyll;hugo</PackageTags>
```

As part of this metadata, you can see that we have added a package icon, and while this is not necessary it just makes our tool's page look nicer. To add this icon, make sure you have an image ready to use. Once you have the image in place, we must add a reference to it in our `.csproj` project:

```xml
<ItemGroup>
    <None Include="icon.png" Pack="true" PackagePath="" />
</ItemGroup>
```

And with that, the metadata for our project is done.

Executing `dotnet pack -c Release` will create a `.nupkg` in the folder we specified earlier within the metadata.

### Publishing to NuGet

For the next step, let's open two tabs in our browser, one in the settings of our repository, specifically in the secrets section. In here we'll need to add a new secret, in the name we'll set `NUGET_KEY` and as for the value... we'll leave it empty for now and move on to our next tab.

It is time we go back to NuGet, and within your account, navigate to API Keys [https://www.nuget.org/account/apikeys](https://www.nuget.org/account/apikeys), create a new one, ideally you'll set more restrictive permissions here, but whatever, we are good for now.

Copy the value of the secret key, go back to the tab with the GitHub secret open, paste it there and save it!

The next step is adding a couple of steps in our `tags.yml` pipeline:

```yml
- name: Build
  run: dotnet pack -c Release

- name: Publish to NuGet
  run: dotnet nuget push -k ${{ secrets.NUGET_KEY }} -s https://www.nuget.org Lockdown/nupkg/
```

Once we have done this, we can go ahead a add new tags to our repo, wait for it to be deployed and then enjoy the fireworks!


```bash
bumpversion patch
git push --follow-tags
```
