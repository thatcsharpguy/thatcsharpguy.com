---
layout: post
title: Trabajando con imágenes e ImageProcessor
date: 2016-07-11 19:00:01
author: Antonio Feregrino
summary: Es muy probable que ya te hayas enfrentado o que tengas que enfrentarte con el trabajo procesar imágenes en un sistema, puede ser algo tan simple como cargar y recortar las imagenes de perfil de usuarios a un sistema o...
featured_image: featured.jpg
tweet_id: 752630903638155264
images_folder: /imageprocessor/
github: https://github.com/ThatCSharpGuy/ImageProcessor-Sample
lang: es
tags: NuGetRecomendado
featured_tag: NuGetRecomendado
---

Hoy toca hablar de otro <a href="http://thatcsharpguy.com/tag/NuGetRecomendado">#NuGetRecomendado</a>. Este es tal vez uno de los más entretenidos que he usado en mi vida, espero que tu también lo encuentres así.  

Es muy probable que ya te hayas enfrentado o que tengas que enfrentarte con este problema en el futuro: Procesamiento de imagenes. Puede ser algo tan simple como cargar y recortar las imagenes de perfil de usuarios a un sistema o aplicar filtos a fotos al más puro estilo de Instagram, como programadores comenzamos a imaginarnos y a investigar cómo podemos realizar esta tareas... o, tal vez busquemos mejor una librería para hacerlo.  

Esta es precisamente una tarea para <a href="http://imageprocessor.org/" target="_blank" rel="nofollow"><i>ImageProcessor</i></a>, una librería para realizar algunas operaciones sobre imagenes con la ayuda de sus métodos, pero vamos a ver cómo se usa:   

## Comenzando  

Vamos a trabajar con esta imagen:  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/balon.jpg" title=""Balón"" />

Para comenzar a trabajar con una imagen, es necesario cargarla y con *ImageProcessor* esta tarea se realiza a través de una instancia de `ImageFactory` y un flujo de datos (en este caso un `MemoryStream`), los colocamos dentro de `using` para facilitarnos el manejo de memoria:

```csharp  
var balonBytes = File.ReadAllBytes("photo/balon.jpg");

using (var inStream = new MemoryStream(balonBytes))
using (var imageFactory = new ImageFactory(false))
{
```  

### Load

Una vez creada la `imageFactory` podemos cargar nuestra imagen usando el método `Load`:

```csharp  
var img = imageFactory.Load(inStream);
```  

### Resize

Una vez cargada, podemos comenzar a aplicar las transformaciones. Por ejemplo, cambiar el tamaño:

```csharp  
img.Resize(new Size(300, 0));
```  

### Save  

Hasta este punto, tenemos la imagen modificada en memoria, así que nuestro `imageFactory` nos provee un método para guardarla en disco:

```csharp  
img.Save("photo/bolaResized.jpg");
```  

Tras lo cual nos queda la siguiente imagen:

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/bolaResized.jpg" title=""Balón"" />

## API fluída

Para nuestra conveniencia, *ImageProcessor* provee una API fluída (fluent API) para encadenar todos los métodos anteriores, por ejemplo, el código anterior se puede reescribir de la siguiente manera:

```csharp  
imageFactory.Load(inStream)
            .Resize(new Size(300, 0))
            .Save("photo/bolaResized.jpg");
```  

### Quality  

Otra de las transformaciones que permite es la de reducir la calidad de una imagen, esto a través del método `Quality` que recibe número con la nueva calidad de la imagen:  

```csharp  
imageFactory.Load(inStream)
            .Quality(5) // Only works with jpg
            .Save("photo/bolaLow.jpg");
```  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/bolaLow.jpg" title=""Balón"" />

### Format  
Trabajar con diversos formatos de archivo a veces puede ser un poco complicado, es por eso que esta librería también permite cambiar el formato de la imagen, por ejemplo, en esta convertimos la imagen de `jpg` a `png`:

```csharp  
imageFactory.Load(inStream)
            .Format(new PngFormat { Quality = 10 })
            .Save("photo/balon.png");
```  

## Transformaciones en la imagen  

Además de cosas como cambio de formatos, de tamaño y de calidad *ImageProcessor* también permite modificar la imagen para aplicarle algunos efectos y transformaciones al contenido, para este ejemplo vamos a trabajar con la foto de la perrita Micha:

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/micha.jpg" title="Micha" />

### Filter  
Al más puro estilo de Instagram, podemos aplicarle filtros a la imagen con el método `Filter`, la librería tiene ya definidos algunos filtos como sepia y escala de grises, entre otros. O si estás muy inspirado, puedes crear el tuyo propio: 

```csharp  
imageFactory.Load(inStream)
            .Filter(MatrixFilters.HiSatch)
            .Save("photo/michaArt.jpg");
```  

Y este es el resultado:  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/michaArt.jpg" title="Micha" />
  
Qué sería de esta ibrería si no diera la capacidad de invertir los colores de una imagen. Esta transformación no requiere de mucha explicación, salvo que se usa el método `Filter` con el filtro `Invert`: 

```csharp  
imageFactory.Load(inStream)
            .Filter(MatrixFilters.Invert)
            .Save("photo/michaInverse.jpg");
```  

Y este es el resultado:  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/michaInverse.jpg" title="Micha" />

### Michstagram (Filter, Tint y Saturation)

Aprovechando que se pueden encadenar varias transformaciones gracias a la API fluída podemos crear un pequeño instagram para Micha:

```csharp  
imageFactory.Load(inStream)
            .Filter(MatrixFilters.Sepia)
            .Tint(Color.LightSalmon)
            .Saturation(50)
            .Save("photo/michaInstagram.jpg");
```  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/michaInstagram.jpg" title="Micha" />

### Más transformaciones  

*ImageProcessor* no se queda ahí, sino que también nos deja recortar recuadros de la imagen, girarla e invertirla respecto a determinado eje. Para la demostración mira la siguiente imagen:

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/motherboard.jpg" title="Motherboard" />

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-3">       
<h3>Crop</h3>
```csharp  
var m = imageFactory.Load(inStream)
        .Crop(new Rectangle(100, 100, 250, 250))
        .Save("photo/motherboardCropped.jpg");
```  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/motherboardCropped.jpg" title="Motherboard" />
</div>
<div class="pure-u-1 pure-u-md-1-3">  
<h3>Rotate</h3>  
```csharp  
m.Rotate(10f)
 .Save("photo/motherboardRotated.jpg");  
 
```  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/motherboardRotated.jpg" title="Motherboard" />
</div>  
<div class="pure-u-1 pure-u-md-1-3">  
<h3>Flip</h3>
```csharp  
m.Flip(true, true)
 .Save("photo/motherboardFlipped.jpg");  
 
```  

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/motherboardFlipped.jpg" title="Motherboard" />
</div>  
</div> 

## Extras  

Usando una combinación de APIs se pueden crear aplicaciones más interesantes, por ejemplo, con el uso de los <a href="https://www.microsoft.com/cognitive-services/en-us/apis" target="_blank" rel="nofollow">Cognitive Services</a> y en especifico, el reconocimiento de caras, se pueden lograr cosas como estas:  


<div class="pure-g">
<div class="pure-u-1 pure-u-md-3-4">
```csharp  
Face face = null;
using (var inStream = new MemoryStream(robbieBytes))
{
    var detectionTask = faceServiceClient.DetectAsync(inStream);
    detectionTask.Wait();
    face = detectionTask.Result.FirstOrDefault();
}

var faceContainer = new Rectangle(face.FaceRectangle.Left, 
    face.FaceRectangle.Top, face.FaceRectangle.Width, face.FaceRectangle.Height);
using (var inStream = new MemoryStream(robbieBytes))
using (var imageFactory = new ImageFactory(false))
{
    imageFactory.Load(inStream)
            .Crop(faceContainer)
            .Save("photo/robbieFace.jpg");
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-4">
<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/robbie3.jpg" title="Robbie Williams" />

<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/robbieFace.jpg" title="Robbie Williams face" />
</div>
</div>     

O mejor aún, podemos detectar las caras aplicarle algunas transformaciones como pixelizarlas: 

```csharp  
Face[] faces;
using (var inStream = new MemoryStream(friendsBytes))
{
    var detectionTask = faceServiceClient.DetectAsync(inStream);
    detectionTask.Wait();
    faces = detectionTask.Result;
}

using (var inStream = new MemoryStream(friendsBytes))
using (var imageFactory = new ImageFactory(false))
{
    var friendsImage = imageFactory.Load(inStream);
    foreach (var f in faces)
    {
        var faceContainer = new Rectangle(f.FaceRectangle.Left, f.FaceRectangle.Top, 
            f.FaceRectangle.Width, f.FaceRectangle.Height);
        friendsImage.Pixelate(20, faceContainer);
    }
    friendsImage.Save("photo/friendsAnonymous.jpg");
}
```  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/friends2.jpg" title="Friends" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/imageprocessor/friendsAnonymous.jpg" title="Friends anon" />
</div>
</div>  

## Conclusión  
No hay mucho que decir sobre *ImageProcessor*, salvo que es una librería muy potente y que nunca está de más contemplarla para proyectos en los que se requiere manipular imágenes. Si bien me parece que podrían ser añadidos otros métodos, los que tiene son de gran provecho. También ten en cuenta que yo no he hablado de todos los métodos disponibles, y te invito a que veas la <a href="http://imageprocessor.org/imageprocessor/imagefactory/" target="_blank" rel="nofollow">documentación completa</a> para conocerlos todos.
 
# Instalación  
Como siempre, hay que buscar en el gestor de paquetes de NuGet: `ImageProcessor`

O desde la consola: 

```  
PM> Install-Package ImageProcessor
```  

No olvides echarle un ojo al <a href="https://github.com/JimBobSquarePants/ImageProcessor" target="_blank" >proyecto en GitHub</a> o en el <a href="http://imageprocessor.org" target="_blank">sitio web del proyecto</a>. Y de nueva cuenta, te invito a <strong><a href="https://github.com/ThatCSharpGuy/ImageProcessor-Sample" target="_blank">descargar el código</a></strong> y hagas pruebas con él.