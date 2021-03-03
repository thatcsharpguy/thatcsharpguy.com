---
layout: post
lannguage: en
title: Creating a dotnet tool (part 4)
date: 2021-01-30 01:00:00
author: Antonio Feregrino
summary: This time I'll show you how to perform work with the file system and write unit tests against it
image: https://i.imgur.com/C0Nqt9A.png
tags: software-engineering, testing, dotnet, c-sharp
---

 > This is a rough draft: explanations are incomplete and there may be spelling mistakes. I just made it public for people that have a hard time copying commands from a video.

If you speak spanish and prefer to see me coding this, you can do so in this video: [Ingeniería de software - Continuous Delivery](https://youtu.be/h8pM1EyOmXQ).

Lockdown is a static website generator written in C# that works as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools).

Through this series of posts I'll guide you on how did I build this tool with the hopes that you will be able to create another *dotnet* tool (hopefully you won't build a better Lockdown competitor, though 😅).

## Topics

 - Add parameters to our build command.  
 - More fun! dependency injection.  
 - Working with the file system.  


## Adding parameters  

The first step is to add parameters to our `BuildCommand`, we need one to tell where our "source" files live, and where should our command output the files once it has finished processing:

```csharp
[Option("-r|--root", Description = "The root path of your website")]
[LegalFilePath]
public string InputPath { get; set; } = "./";

[Option("-o|--out", Description = "The output directory where your built website will be stored")]
[LegalFilePath]
public string OutputPath { get; set; } = "./_site";

public int OnExecute()
{
    this.console.WriteLine($"Source directory: {this.InputPath}");
    this.console.WriteLine($"Output path: {this.OutputPath}");
    return 0;
}
```

And when we execute our tool, we'll see our new output (we should probably modify our tests, but because of time, we won't):

```bash
dotnet run --project Lockdown/Lockdown.csproj -- build
# dotnet run --project Lockdown/Lockdown.csproj -- build -r site -o ../hey
```  

# Introducing a layer of abstraction  

Think about it, we don't really want to tie the implementation of the site building directly to the `BuildCommand`, the this build command should be nothing but an interface to the actual site building, we need to put the code in a separate space. Later, if we want to move on from a conmmand line tool to a GUI, instead of having to move our site building code from our command line code, we would simply need to pass a reference to this code to our new user interface. To do this, let's add a new interface called `ISiteBuilder` (we'll add this under a new namespace called `Build`), this interface would implement a single method, at least for now:

```csharp
namespace Lockdown.Build
{
    public interface ISiteBuilder
    {
        public void Build(string inputPath, string outputPath);
    }
}
```

And let's write a simple implementation for it, we'll have to modify it later though!

```csharp
namespace Lockdown.Build
{
    public class SiteBuilder : ISiteBuilder
    {
        public SiteBuilder()
        {
        }

        public void Build(string inputPath, string outputPath)
        {
        }
    }
}
```

Lastly, we just need to add it to our DI container:

```csharp
ServiceProvider services = new ServiceCollection()
    .AddSingleton<ISiteBuilder, SiteBuilder>()
    .AddSingleton<IConsole>(PhysicalConsole.Singleton)
    .BuildServiceProvider();
```

And then we can use it in our `BuildCommand`:

```csharp
private readonly IConsole console;
private readonly ISiteBuilder siteBuilder;

public BuildCommand(IConsole console, ISiteBuilder siteBuilder)
{
    this.console = console;
    this.siteBuilder = siteBuilder;
}


public int OnExecute()
{
    this.console.WriteLine($"Source directory: {this.InputPath}");

    this.siteBuilder.Build(this.InputPath, this.OutputPath);

    // ...
```

## *Moq*ing interfaces

Now, we should test our code! the test is quite straightforward, we just need to guarantee our method `OnExecute` calls the method `Build` on our `ISiteBuilder` instance. We'll use a tiny little library that will help us to create a "fake" version of an `ISiteBuilder`:

```bash
dotnet add Lockdown.Test/Lockdown.Test.csproj package Moq 
```

Once we added this NuGet, we can change our testing code:

```csharp
using Moq;

// ... 

[Fact]
public void TestOnExecute()
{
    // Setup
    var testConsole = new TestConsole();
    var mockSiteBuilder = new Mock<ISiteBuilder>();
    var inputFolder = "./input/path";
    var outputFolder = "/output/path";

    var buildCommand = new BuildCommand(testConsole, mockSiteBuilder.Object);
    buildCommand.InputPath = inputFolder;
    buildCommand.OutputPath = outputFolder;

    var expectedOutput = $"Source directory: {inputFolder}" + Environment.NewLine +
                            $"Output path: {outputFolder}" + Environment.NewLine;

    // Act
    buildCommand.OnExecute();

    // Assert
    mockSiteBuilder.Verify(siteBuilder => siteBuilder.Build(inputFolder, outputFolder), Times.Once);

    string writtenText = testConsole.GetWrittenContent();
    writtenText.ShouldBe(expectedOutput);
}
```

# File systems and dependency injection

Let's move on and have a look at our `SiteBuilder` class. First things first, we should ensure that the output path gets cleaned before processing, and it exists after processing, to do this we are going to interact with the file system. 

To do this we could simply use the classes available in the `System.IO` namespace, but this is not test friendly! we don't really want to be messing with the file system during our tests! remember how did we solve the problem of interacting with the console? Yes, dependency injection. Let's add a NuGet that will help our development efforts.

```bash
dotnet add Lockdown/Lockdown.csproj package System.IO.Abstractions
```

This package offers, among other things, an interface called `IFileSystem` that we can use to interact with the file system objects, and of course, we should inject this dependency into our `SiteBuilder` via its constructor:


```csharp
private readonly IFileSystem fileSystem;

public SiteBuilder(IFileSystem fileSystem)
{
    this.fileSystem = fileSystem;
}
```

Obviously, like all our dependencies, we need to add it to the dependency container:

```csharp
ServiceProvider services = new ServiceCollection()
    .AddSingleton<IFileSystem, FileSystem>()
    .AddSingleton<ISiteBuilder, SiteBuilder>()
    .AddSingleton<IConsole>(PhysicalConsole.Singleton)
```

Let's start coding our *site builder*, the first thing we care about is to to clean whatever is in the output folder if it exists (though we must warn our user this is gonig to happen!) or to create a new one if it does not exists. 

```csharp
public void Build(string inputPath, string outputPath)
{
    this.CleanFolder(outputPath);
}

public virtual void CleanFolder(string folder)
{
    // To be implemented  
}
```

Notice the `virtual` method in `CleanFolder`?

Before we implement our `CleanFolder`, should we do some TDD? let's do it. To test this using our brand new `System.IO.Abstractions` package we must add yet another package:

```bash
dotnet add Lockdown.Test/Lockdown.Test.csproj package System.IO.Abstractions.TestingHelpers 
```

Let's add a couple of test methods, one that will test for the case when the output directory does not exist, and another that will test when it exists and contains a file:

```csharp
using System.IO;
using System.IO.Abstractions;
using System.IO.Abstractions.TestingHelpers;
using System.Linq;
using Lockdown.Build;
using Shouldly;
using Xunit;

namespace Lockdown.Test
{
    public class SiteBuilderTests
    {
        public IFileSystem fakeFileSystem;

        private const string inputPath = "./some/input";
        private const string outputPath = "./some/output";

        public SiteBuilderTests()
        {
            this.fakeFileSystem = new MockFileSystem();

        }

        [Fact]
        public void TestOutputFolderDoesNotExist()
        {
            // Setup
            var siteBuilder = new SiteBuilder(this.fakeFileSystem);

            // Act
            siteBuilder.Build(inputPath, outputPath);


            // Assert
            this.fakeFileSystem.Directory.Exists(outputPath).ShouldBeTrue();
            this.fakeFileSystem.Directory.EnumerateFiles(outputPath).Any().ShouldBeFalse();

        }

        [Fact]
        public void TestOutputFolderHasFiles()
        {
            // Setup
            var someFilePath = Path.Combine(outputPath, "file.txt");
            this.fakeFileSystem.Directory.CreateDirectory(outputPath);
            this.fakeFileSystem.File.WriteAllText(someFilePath, "hello world");
            var siteBuilder = new SiteBuilder(this.fakeFileSystem);

            // Act
            siteBuilder.Build(inputPath, outputPath);


            // Assert
            this.fakeFileSystem.Directory.Exists(outputPath).ShouldBeTrue();
            this.fakeFileSystem.Directory.EnumerateFiles(outputPath).Any().ShouldBeFalse();

        }
    }
}
```

If we now test with `dotnet test` everything should fail:

```bash
dotnet test
```

Then we can actually use our field `fileSystem` in our `SiteBuilder` implementation:

```csharp
public void Build(string inputPath, string outputPath)
{
    if (this.fileSystem.Directory.Exists(outputPath))
    {
        this.fileSystem.Directory.Delete(outputPath, recursive: true);
    }

    this.fileSystem.Directory.CreateDirectory(outputPath);
}
```

We could add a test asserting that we call `CleanFolder` when building a new site:

```csharp
[Fact]
public void TestCallsMethodsWhenBuilding()
{
    // Setup
    var mockSiteBuilder = new Mock<SiteBuilder>(MockBehavior.Loose, this.fakeFileSystem);
    mockSiteBuilder.Setup(siteBuilder => siteBuilder.CleanFolder(outputPath));

    // Act
    mockSiteBuilder.Object.Build(inputPath, outputPath);

    // Assert
    mockSiteBuilder.Verify(siteBuilder => siteBuilder.CleanFolder(outputPath));
}
```

## Test copying files over  

```csharp
[Fact]
public void TestCopyFiles()
{
    // Setup
    var stylesFile = Path.Combine(inputPath, "style.css");
    var someOtherFile = Path.Combine(inputPath, "subfolder", "style.css");

    var contents = new Dictionary<string, MockFileData>
    {
        { stylesFile, new MockFileData("body { color: #fff; }") },
        { someOtherFile, new MockFileData("more data") }
    };

    var fakeFileSystem = new MockFileSystem(contents);
    fakeFileSystem.Directory.CreateDirectory(outputPath);
    var siteBuilder = new SiteBuilder(fakeFileSystem);

    // Act
    siteBuilder.CopyFiles(inputPath, outputPath);

    // Assert
    fakeFileSystem.Directory.EnumerateFiles(outputPath, "*.*", SearchOption.AllDirectories).Count().ShouldBe(2);
}
```

And the implementation:

```csharp
public virtual void CopyFiles(string input, string output)
{
    var source = this.fileSystem.DirectoryInfo.FromDirectoryName(input);
    var target = this.fileSystem.DirectoryInfo.FromDirectoryName(output);

    this.CopyFiles(source, target);
}

private void CopyFiles(IDirectoryInfo source, IDirectoryInfo target)
{
    foreach (var fi in source.GetFiles())
    {
        fi.CopyTo(Path.Combine(target.FullName, fi.Name));
    }

    foreach (var subDirectory in source.GetDirectories())
    {
        var nextTargetSubDir = target.CreateSubdirectory(subDirectory.Name);
        this.CopyFiles(subDirectory, nextTargetSubDir);
    }
}
```  

We can also assert that we call `CopyFiles` in our build method:

```csharp
[Fact]
public void TestCleanBuilderTestHasFiles()
{
    // Setup
    var someFilePath = Path.Combine(outputPath, "file.txt");
    this.fakeFileSystem.Directory.CreateDirectory(outputPath);
    this.fakeFileSystem.File.WriteAllText(someFilePath, "hello world");
    var siteBuilder = new SiteBuilder(this.fakeFileSystem);

    // Act
    siteBuilder.CleanFolder(outputPath);


    // Assert
    this.fakeFileSystem.Directory.Exists(outputPath).ShouldBeTrue();
    this.fakeFileSystem.Directory.EnumerateFiles(outputPath).Any().ShouldBeFalse();

}
```