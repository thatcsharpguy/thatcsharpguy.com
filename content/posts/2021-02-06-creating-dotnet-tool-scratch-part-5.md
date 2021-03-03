---
layout: post
lannguage: en
title: Creating a dotnet tool (part 5)
date: 2021-02-06 01:00:00
author: Antonio Feregrino
summary: This time I'll show you how to work with liquid templates and how to run a local webserver to test our creation
image: https://i.imgur.com/C0Nqt9A.png
tags: software-engineering, testing, dotnet, c-sharp
---

 > This is a rough draft: explanations are incomplete and there may be spelling mistakes. I just made it public for people that have a hard time copying commands from a video.

If you speak spanish and prefer to see me coding this, you can do so in this video: [Ingeniería de software - Continuous Delivery](https://youtu.be/h8pM1EyOmXQ).

Lockdown is a static website generator written in C# that works as a [.NET tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools).

Through this series of posts I'll guide you on how did I build this tool with the hopes that you will be able to create another *dotnet* tool (hopefully you won't build a better Lockdown competitor, though 😅).

## Topics

 - Jinja templating
 - Liquid documents
 - Web server

The way static website generators work is pretty straightforward, more or less like a compiler: there are some source files that we need to read and process in order to generate an output. These files are often markdown or html files (but you can customise this settng later).

Another powerful tool of the static website generators is the posibility of creating and sharing templates across diferent pages, and ours will not be the exception. To start adding support for all these functionalities, let's add some NuGet packages:  

```bash
dotnet add Lockdown/Lockdown.csproj package DotLiquid
dotnet add Lockdown/Lockdown.csproj package Slugify.Core
```

## Adding some simple templates  

It is not my intention to teach Liquid nor Markdown, so [here are some docs for Liquid](https://shopify.github.io/liquid/).  

To see the packages we just installed in action, let's add some simple html files into a new path called `Lockdown/demo/templates`.

### `_base.liquid`

{% raw %}
```html
<html>
    <head>
        <title>{{ title }}</title>
    </head>
	<body>
        {% block content %}
        {% endblock %}
    </body>
</html>
```
{% endraw %}

### `_post.liquid`

{% raw %}
```html
{% extends 'base' %}

{% block content %}
<h1>{{ title }}</h1>
<p>Date {{ date | date: "MMMM dd, yyyy" }}</p>
{% block post_content %}
{% endblock %}
{% endblock %}
```
{% endraw %}

## Adding some sample posts  

Let's add some sample posts, we are going to do this into the folder `Lockdown/demo/posts`:

### `post-one.md`  

```text
---
title: First post
date: 2021-02-06
---

<i>Hello world</i>!

This is my <b>first post</b>!
```

### `post-two.md`  

```text
---
title: Second post
date: 2021-02-07
---

¡Buen día señor sol!
```

Now, let's add a method that will allow us to read all the file contentsfrom within the `posts` folder:,

```csharp
public virtual IEnumerable<string> GetPosts(string inputPath)
{
    // ...
}
```

You know the drill, let's add some tests first:

```csharp
[Theory]
[InlineData(0)]
[InlineData(1)]
[InlineData(10)]
public void TestGetPostsWithSinglePost(int files)
{
    var postsPath = this.fakeFileSystem.Path.Combine(inputPath, "posts");
    this.fakeFileSystem.Directory.CreateDirectory(postsPath);
    var fileContents = new List<string>();
    for (var i  = 0; i < files; i++)
    {
        var postPath = this.fakeFileSystem.Path.Combine(postsPath, $"file_{i}.txt");
        var content = "# Hola Mundo!\n\n**Prueba {i}**";
        this.fakeFileSystem.File.WriteAllText(postPath, content);
        fileContents.Add(content);
    }
    var siteBuilder = new SiteBuilder(this.fakeFileSystem);

    var posts = siteBuilder.GetPosts(inputPath);

    posts.OrderBy(content=>content).ShouldBe(fileContents);
}
```

Now, on to the implementation:

```csharp
public virtual IEnumerable<string> GetPosts(string inputPath)
{
    var inputPostsPath = this.fileSystem.Path.Combine(inputPath, PostsPath);
    if (this.fileSystem.Directory.Exists(inputPostsPath))
    {
        foreach (var file in this.fileSystem.Directory.EnumerateFiles(inputPostsPath, "*.*", SearchOption.AllDirectories))
        {
            yield return this.fileSystem.File.ReadAllText(file);
        }
    }
}
```

We now have something that reads files from disk, it is time for us to do something about them; let's add a method that is going to split our post between the metadata and the content:


```csharp
public virtual Tuple<string, string> SplitPost(string post)
{
    var metadatStringBulder = new StringBuilder();
    var contentStringBuilder = new StringBuilder();
    int separators = 0;
    const string separator = "---";
    foreach (var line in post.Split(Environment.NewLine))
    {
        if (separators == 2)
        {
            contentStringBuilder.Append(line).AppendLine();
        }
        else if (line == separator)
        {
            separators += 1;
        }
        else
        {
            metadatStringBulder.Append(line).AppendLine();
        }
    }

    return Tuple.Create(metadatStringBulder.ToString(), contentStringBuilder.ToString());
}
```

To contain the metadata, we should add a class called `RawPostMetadata` to give this metadata a bit more shape:

```csharp
namespace Lockdown.Build.Entities
{
    using System;

    public class RawPostMetadata
    {
        public string Title { get; set; }

        public DateTime Date { get; set; }
    }
}
```

Now we can add a method to transform between a string and our new `RawPostMetadata`:

```csharp
public virtual RawPostMetadata ConvertMetadata(string metadata)
{
    var metadataEntries = metadata
        .Split(Environment.NewLine)
        .Where(line => !string.IsNullOrEmpty(line))
        .Select(line => line.Split(':', 2))
        .Select(parts => KeyValuePair.Create(parts[0].Trim().ToLower(), parts[1].Trim()));

    var dict = new Dictionary<string, string>(metadataEntries);

    return new RawPostMetadata
    {
        Title = dict["title"],
        Date = DateTime.Parse(dict["date"]),
    };
}
```

### Templating

Now, we are almost ready to perform the templating using `DotLiquid`, but first, given that we have introduced a new way to integrate with the file system, we need to add a helper class that will allow `DotLiquid` to interact with it. Add a new class `HelperFileSystem` in the `Build` namespace:

```csharp
namespace Lockdown.Build
{
    using System.IO.Abstractions;
    using DotLiquid;

    public class HelperFileSystem : DotLiquid.FileSystems.IFileSystem
    {
        public LockdownFileSystem(IFileSystem fileSystem, string rootPath)
        {
            this.FileSystem = fileSystem;
            this.RootPath = rootPath;
        }

        public IFileSystem FileSystem { get; }

        public string RootPath { get; }

        public string ReadTemplateFile(Context context, string templateName)
        {
            var templatePath = this.FileSystem.Path.Combine(this.RootPath, $"_{templateName}.liquid");
            return this.FileSystem.File.ReadAllText(templatePath);
        }
    }
}
```


Then we can mode on to implement our method called `ConvertContent` that will receive the metadata por the post, the actual post content, and our input path (to find the templates):

{% raw %}
```csharp
public virtual string RenderContent(RawPostMetadata metadata, string content, string inputPath)
{
    var templatesPath = this.fileSystem.Path.Combine(inputPath, "templates");
    Template.FileSystem = new LockdownFileSystem(this.fileSystem, templatesPath);

    var contentWrapped = @"{% extends post %}
{% block post_content %}
" + content + @"
{% endblock %}";

    var template = Template.Parse(contentWrapped);

    var postVariables = new
    {
        title = metadata.Title,
        date = metadata.Date,
    };

    var renderedContent = template.Render(Hash.FromAnonymousObject(postVariables));

    return renderedContent;
}
```
{% endraw %}

Then we can write a test for it, but since we want to assert that we generated proper html, let's add a new NuGet package that will help us precisely with this:

```bash
dotnet add Lockdown.Test/Lockdown.Test.csproj package AngleSharp
```

And then we can move on to our test implementation:

```csharp
private async Task<IDocument> ParseHtml(string document)
{
    var context = BrowsingContext.New(Configuration.Default);
    return await context.OpenAsync(req => req.Content(document));
}

[Fact]
public async Task TestRenderContent()
{
    // Setup: Prepare our test by copying our `demo` folder into our "fake" file system.
    var workspace = Path.GetFullPath(Path.Combine(Directory.GetCurrentDirectory(), "../../../../"));
    var templatePath = Path.Combine(workspace, "Lockdown", "demo", "templates");
    var dictionary = new Dictionary<string, MockFileData>();
    foreach (var path in Directory.EnumerateFiles(templatePath))
    {
        var fakePath = path.Replace(templatePath, Path.Combine(inputPath, "templates"));
        dictionary.Add(fakePath, new MockFileData(File.ReadAllBytes(path)));
    }
    var metadata = new RawPostMetadata { Title = "Test post", Date = new DateTime(2000, 1, 1) };
    var postContent = "Hola Mundo!" + Environment.NewLine + "Hola";
    var siteBuilder = new SiteBuilder(new MockFileSystem(dictionary));

    // Act
    var converted = siteBuilder.ConvertContent(metadata, postContent, inputPath);

    // Asserts
    var html = await ParseHtml(converted);

    var h1 = html.All.First(node => node.LocalName == "h1");
    h1.TextContent.Trim().ShouldBe("Test post");
}
```

Now if we test:

```bash
dotnet test
```

Now, we should be all set to write the result of this conversion to the output folder; however, to select the name of the output file, we are going to use the *slugify* package we installed at the beginning of the post, and putting everything together, this is how our new `Build` method looks like:

```csharp
public void Build(string inputPath, string outputPath)
{
    this.CleanFolder(outputPath);
    this.CopyFiles(inputPath, outputPath);
    var posts = this.GetPosts(inputPath);
    SlugHelper helper = new SlugHelper();
    foreach (var post in posts)
    {
        var parts = this.SplitPost(post);
        var metadata = this.ConvertMetadata(parts.Item1);
        var renderedContent = this.ConvertContent(metadata, parts.Item2, inputPath);

        var postSlug = helper.GenerateSlug(metadata.Title);
        var outputFilePath = this.fileSystem.Path.Combine(outputPath, $"{postSlug}.html");

        this.fileSystem.File.WriteAllText(outputFilePath, renderedContent);
    }
}
```

Now, for a quick test:

```bash
dotnet run --project Lockdown/Lockdown.csproj -- build -r Lockdown/demo/ -o _sitio
```

## Web server!

### Add NuGet packages  

```bash
dotnet add Lockdown/Lockdown.csproj package Microsoft.AspNetCore.Hosting.Abstractions
dotnet add Lockdown/Lockdown.csproj package Microsoft.AspNetCore.Server.Kestrel
dotnet add Lockdown/Lockdown.csproj package Microsoft.AspNetCore.StaticFiles
```

### Add a new command

 - Parameters
 - Add site builder class

### Add a RunCommand

```csharp
namespace Lockdown.Commands
{
    using Lockdown.Build;
    using McMaster.Extensions.CommandLineUtils;

    public class RunCommand
    {
        private readonly ISiteBuilder siteBuilder;

        public RunCommand(ISiteBuilder siteBuilder)
        {
            this.siteBuilder = siteBuilder;
        }

        [Option("-r|--root", Description = "The root path of your website")]
        [LegalFilePath]
        public string InputPath { get; set; } = "./";

        [Option("-o|--out", Description = "The output directory where your built website will be stored")]
        [LegalFilePath]
        public string OutputPath { get; set; } = "./_site";

        public int OnExecute()
        {
            return 0;
        }
    }
}
```
 
Call the `Build` method inside our execute method:

```csharp
this.siteBuilder.Build(this.InputPath, this.OutputPath);
```

Add server serving to our `OnExecute`

```csharp
var webRoot = this.fileSystem.Path.Combine(this.fileSystem.Directory.GetCurrentDirectory(), this.OutputPath);

var settings = new Dictionary<string, string>
{
    { "webRoot", webRoot },
};
var configBuilder = new ConfigurationBuilder();
configBuilder.AddInMemoryCollection(settings);

var host = new WebHostBuilder()
    .UseConfiguration(configBuilder.Build())
    .UseStartup<Startup>()
    .UseKestrel()
    .UseUrls($"http://*:5000")
    .Build();

host.Run();
```

### Add `Startup` class

```csharp
namespace Lockdown.Run
{
    using Microsoft.AspNetCore.Builder;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.FileProviders;

    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            this.Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        public void Configure(IApplicationBuilder app)
        {
            app.UseFileServer(new FileServerOptions()
            {
                FileProvider = new PhysicalFileProvider(this.Configuration.GetValue<string>("webRoot")),
            });
        }
    }
}
```

### Add the command to our server options

```csharp
[Subcommand(typeof(RunCommand))]
```

And, finally... run:

```bash
dotnet run --project Lockdown/Lockdown.csproj -- run -r Lockdown/demo/ -o _sitio
```

And navigate to `http://127.0.0.1:5000/first-post.html` to see your creation.
