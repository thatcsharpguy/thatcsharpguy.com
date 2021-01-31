---
layout: post
title: Mejores imágenes en Xamarin.Forms
date: 2017-05-23 19:00:01
author: Antonio Feregrino
summary: Ahorra recursos al cargar imágenes en tus aplicaciones hechas con Xamarin.Forms con este control... que probablemente no tenías idea que necesitabas.
featured_image: featured.png
images_folder: /nugets/ffimage/
github: https://github.com/ThatCSharpGuy/ffimageloading-sample
lang: es
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

¿Has tenido que lidiar con imágenes en tu aplicación? ¿has tenido que usarlas dentro de alguna lista? Este *plugin* es un control que nos ayudará a trabajar con imágenes de todo tipo, sí, de todo tipo, incluido *SVG* y *WebP*, en nuestras aplicaciones.  

## El control `CachedImage`  

Una de las mejores herramientas que nos ofrece es el control `CachedImage` que tiene un nombre muy descriptivo puesto que es justamente eso: Una imagen que es *cacheable*.

La posibilidad de *cachear* las imágenes que muestra este control puede ser modificada mediante su propiedad `CacheType`, por default las imágenes se almacenan temporalmente en el disco y en la memoria. Esto funciona tanto para imágenes en línea como para las que están en el dispositivo. Esto quiere decir que si dos o más controles está utilizando el mismo recurso este se compartirá, en lugar de ser cargado dos veces.  

Otra de sus características es la que de-duplica las peticiones hechas a una misma imagen en internet. Es decir, si varios controles quieren descargar una misma imagen, esta solo se descarga una vez y se hace disponible a los otros.  

Una buena manera de observar este par de características es cargar varias imágenes en una lista y ver su comportamiento, en el siguiente video hay tres pantallas:  

La de la izquierda hace uso del control por default de Xamarin con el cacheo apagado, la central usa el control de Xamarin con las opciones por default, y la tercera muestra el control CachedImage de FFImageLoading:

<div style="max-width: 500px; margin: 0 auto;">
<div class="video-wrapper">
    <iframe width="992" height="558" src="https://www.youtube.com/embed/19eanlakPn8" frameborder="0" allowfullscreen></iframe>
</div>
</div>  

En realidad, tan solo con la *caché* de imágenes, este plugin ya vale la pena, pero hay más. 

## Imágenes de carga y error  

Si deseas mostrarles a tus usuarios un indicador de que la imagen que se mostrará está cargándose puedes usar la propiedad `LoadingPlaceholder`. Y bueno, nunca está de más planear para los errores, entonces también puedes usar la propiedad `ErrorPlaceholder` para colocar un respaldo si la carga de una imagen falla:  

<div style="max-width: 500px; margin: 0 auto;">
<div class="video-wrapper">
    <iframe width="992" height="558" src="https://www.youtube.com/embed/NCJXtT_dGtQ" frameborder="0" allowfullscreen></iframe>
</div>
</div>

Oh, ¿viste ese efecto en la pantalla justo antes de que apareció el perrito? esa animación también es parte del control y se puede deshabilitar con la propiedad `FadeAnimationEnabled`. Y, volviendo a los errores: Si la carga de una imagen falla, se puede establecer un contador de reintentos para volver a cargarla, para eso están las propiedades `RetryCount` y `RetryDelay`.

## Transformaciones  

Adicionalmente podemos habilitar las transformaciones para modificar las imágenes, y se le pueden agregar varias transformaciones a una misma imagen, de tal modo que cuando ejecutamos las siguientes líneas:   

```csharp  
var grayscale = new GrayscaleTransformation();
var blur = new BlurredTransformation(5);
var corners = new CornersTransformation(4, CornerTransformType.AllCut);
Image.Transformations.Add(grayscale);
Image.Transformations.Add(blur);
Image.Transformations.Add(corners);
```  

La imagen se transforma en:  

<img src="https://thatcsharpguy.github.io/postimages/nugets/ffimage/transformed.jpg" title="Imagen transformada" />

(Puedes descargar el <a href="https://github.com/ThatCSharpGuy/ffimageloading-sample" target="_blank">código de la app demo</a> en GitHub, tendrás que obtener una clave de API en <a href="https://pixabay.com/api/docs/" target="_blank">Pixabay</a>)

## Instalación  
Para instalarlo basta con instalar el <a href="https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms" target="_blank">paquete de NuGet</a> Xamarin.FFImageLoading.Forms. Y si vas a usar el soporte SVG y las transformaciones debes instalar <a href="https://www.nuget.org/packages/Xamarin.FFImageLoading.Svg.Forms" target="_blank">Xamarin.FFImageLoading.Svg.Forms</a> y <a href="https://www.nuget.org/packages/Xamarin.FFImageLoading.Transformations" target="_blank">Xamarin.FFImageLoading.Transformations</a>.

```  
PM> Install-Package Xamarin.FFImageLoading.Forms
PM> Install-Package Xamarin.FFImageLoading.Svg.Forms
PM> Install-Package Xamarin.FFImageLoading.Transformations
```  

Y como con muchos *plugins*, es necesario instalarlo tanto en el proyecto central como en los clientes, e inicializarlo antes de cargar la aplicación con:  

```csharp  
CachedImageRenderer.Init();
```  

Al ser código abierto puedes revisar todos los detalles en <a href="https://github.com/luberda-molinet/FFImageLoading" target="_blank">GitHub</a>.