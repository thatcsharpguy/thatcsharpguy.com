---
layout: post
lannguage: en
title: Creating a dotnet tool (part 2)
date: 2021-01-16 01:00:00
author: Antonio Feregrino
summary: In this entry I'll show you how to add som basic unit testing, as well as how to use Dependency Injection to enhance our tool.
image: https://i.imgur.com/C0Nqt9A.png
tags: software-engineering, testing, dotnet, c-sharp
---

 > This is a rough draft: explanations are incomplete and there may be spelling mistakes. I just made it public for people that have a hard time copying commands from a video.

If you speak spanish and prefer to see me coding this, you can do so in this video: [Ingeniería de software  - Comenzando con nuestro proyecto](https://youtu.be/npUrHUyeMc4).

Lockdown is a static website generator written in C# that works as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools).

Through this series of posts I'll guide you on how did I build this tool with the hopes that you will be able to create another *dotnet* tool (hopefully you won't build a better Lockdown competitor, though 😅).



Lockdown is a static website generator written in C# that works as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools).

Through this series of posts I'll guide you on how did I build this tool with the hopes that you will be able to create another *dotnet* tool (hopefully you won't build a better Lockdown competitor, though 😅).

## Some boilerplate code

```csharp
public static void Main(string[] args)
{
    var arguments = string.Join(";", args);
    var currentDirectory = Directory.GetCurrentDirectory(); // using System.IO;
    Console.WriteLine(arguments);
    Console.WriteLine(currentDirectory);
}
```

## Running our tool  

**From the console**: `dotnet run --project Lockdown/Lockdown.csproj -- argumento1 argumento2`

## CommandLineUtils  

While we can parse the arguments passed over our program, it is probably best if we make use of a third-party tool that does this magic for us, there are many alternatives, but the one I'll be using is [CommandLineUtils](https://github.com/natemcmaster/CommandLineUtils):

```sharp
dotnet add Lockdown/Lockdown.csproj package McMaster.Extensions.CommandLineUtils
```

### Modifications to our entry point

Next up for us is to star using our brand new installed package, we'll do so by modifying our class `Program.cs`:

```csharp
using McMaster.Extensions.CommandLineUtils;

[Command("lockdown")]
[VersionOptionFromMember("--version", MemberName = nameof(LockdownVersion))]
public class Program
{
    public string LockdownVersion { get; } = "0.0.0";

    public static void Main(string[] args) => CommandLineApplication.Execute<Program>(args);

    public int OnExecute(CommandLineApplication app)
    {
        app.ShowHelp();
        return 0;
    }
}
``` 

We added the using... added the property LockdownVersion, changed the Main method, added OnExecute.

Now, if we run our program we'll see that we get some nice looking UI:

```shell
dotnet run --project Lockdown/Lockdown.csproj --
```

### An experiment, let's make a calculator

We can then move on to specify the arguments to our program using properties

```csharp
[Argument(0, Description = "The first number")]
[Required]
public int FirstNumber { get; set; }

[Argument(1, Description = "The opertion to perform")]
[Required]
public string Operation { get; set; }

[Argument(2, Description = "The second number")]
[Required]
public int SecondNumber { get; set; }
```

With that, we could perform the following changes to our `OnExecute` method:

```csharp
public int OnExecute(CommandLineApplication app)
{
    switch (this.Operation)
    {
        case "+":
            Console.WriteLine(this.FirstNumber + this.SecondNumber);
            break;
        case "-":
            Console.WriteLine(this.FirstNumber - this.SecondNumber);
            break;
        case "/":
            Console.WriteLine(this.FirstNumber / this.SecondNumber);
            break;
        case "*":
            Console.WriteLine(this.FirstNumber * this.SecondNumber);
            break;
        default:
            Console.WriteLine($"The operation {this.Operation} is not yet implemented");
            return 1;
    }

    return 0;
}
```

Now we could use our tool like this:

```bash
dotnet run --project Lockdown/Lockdown.csproj -- 0 - 10
```

Having taken this detour, let's erase all we made and go on with our actual work. Check more samples and [documentation here](https://natemcmaster.github.io/CommandLineUtils/), though the [samples directory](https://github.com/natemcmaster/CommandLineUtils/tree/main/docs/samples).

## Using subcommands

As I mentioned early, our tool should offer two options: `build` and `serve`, we'll implement these as subcommands. Our fancy new package allows us to implement these right out of the box.

To keep things a bit tidy, let's add a folder called `Commands`, and inside it a file called `BuildCommand.cs`:

```csharp
namespace Lockdown.Commands
{
    using System;
    using McMaster.Extensions.CommandLineUtils;

    public class BuildCommand
    {
        public int OnExecute(CommandLineApplication app)
        {
            Console.WriteLine("this is the build command");
            return 0;
        }
    }
}
```   

The next step is adding the reference to this new command to our main program:

```csharp
using Lockdown.Commands;
using McMaster.Extensions.CommandLineUtils;

[Command("lockdown")]
[VersionOptionFromMember("--version", MemberName = nameof(LockdownVersion))]
[Subcommand(typeof(BuildCommand))]
public class Program
{
	// ...
```

And if we try to execute our tool from the command line, we are going to see some new content:

```bash
dotnet run --project Lockdown/Lockdown.csproj --
```

And now we have the build command available to us:

```bash
dotnet run --project Lockdown/Lockdown.csproj -- build
```

## Adding dependency injection

We need to add a new NuGet package:

```bash
dotnet add Lockdown/Lockdown.csproj package Microsoft.Extensions.DependencyInjection
```  

We need to add a `using`:

```csharp
using Microsoft.Extensions.DependencyInjection;
```

And replace the code in `Program` for the following, where we set up our dependency injection container:

```csharp
public static int Main(string[] args)
{
    var services = new ServiceCollection()
        .AddSingleton<IConsole>(PhysicalConsole.Singleton)
        .BuildServiceProvider();

    var app = new CommandLineApplication<Program>();
    app.Conventions
        .UseDefaultConventions()
        .UseConstructorInjection(services);

    return app.Execute(args);
}
```  

Now we can replace our `Console.WriteLine` with whatever our dependency injection container give us.  

```csharp
public class BuildCommand
{
    private readonly IConsole console;

    public BuildCommand(IConsole console)
    {
        this.console = console;
    }

    public int OnExecute(CommandLineApplication app)
    {
        this.console.WriteLine("this is the build command");
        return 0;
    }
}
```

Now we can run our tool again, and bam. Nothing has changed:

```bash
dotnet run --project Lockdown/Lockdown.csproj -- build
```

## Testing  

So, now we can move on to the test our code, though in the future we'd try to write the tests first and then implement the code.

Taking advantage of the brand new interfaces and dependency injection we will be creating a teeny tiny class that will help us to test our interaction with the console:

```csharp
namespace Lockdown.Test.Utils
{
    using System;
    using System.IO;
    using McMaster.Extensions.CommandLineUtils;

    public class TestConsole : IConsole
    {
        public TestConsole()
        {
            this.outStream = new MemoryStream();
            this.Out = new StreamWriter(this.outStream);
        }

        public TextWriter Out
        {
            get;
            private set;
        }

        public string GetWrittenContent()
        {
            this.Out.Flush();
            this.outStream.Flush();
            this.outStream.Seek(0, SeekOrigin.Begin);
            StreamReader reader = new StreamReader(this.outStream);
            return reader.ReadToEnd();
        }

        private readonly MemoryStream outStream;

		// rest of non implemented members...
    }
}
```

Again, for the sake of order, let's rename our `UnitTest1.cs` class to something more meaningful like `BuildCommandTests.cs`, while we are at it let's replace the base method for something that test our on execute method:

```csharp
using Lockdown.Commands;
using Lockdown.Test.Utils;
using Xunit;

namespace Lockdown.Test
{
    public class BuildCommandTests
    {
        [Fact]
        public void TestWriteToConsole()
        {
            var testConsole = new TestConsole();
            var buildCommand = new BuildCommand(testConsole);

            buildCommand.OnExecute();

            var writtenContent = testConsole.GetWrittenContent();

            Assert.Equal("this is the build command\n", writtenContent);

        }
    }
}
```

### A bonus!  

We have seen some assert over there, and assert is what we do in unit tests. But we can make our assertions more understandable by using yet another NuGet package called *Shouldly*:

```sharp
dotnet add Lockdown.Test/Lockdown.Test.csproj package Shouldly
```

This should allow us to replace our old ancient `Assert` for this brand new modern *Shouldly-assert*:

```csharp
writtenContent.ShouldBe("this is the serve command\n");
````

## Merging

Now that we have testing up and running, we can merge our branch into `main`, however, what good are our tests if they don't get run automatically in our CI pipeline?

Let's change that with a few lines of YAML code. We simply need to add:

```yaml
- name: Test
  run: |
    dotnet test
```

As one of our steps in our CI pipeline and voilà, that is it.

We can then go onto GitHub and create a merge request, wait for the checks to complete, have someone review our code, and finally, integrate our code into main.
