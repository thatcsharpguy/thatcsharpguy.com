---
layout: post
title: ¿Qué es una Portable Class Library?
date: 2015-11-02 10:00:00
author: Antonio Feregrino
categories: c-sharp visual-studio
summary: Las bibliotecas de clases portables nos permiten compartir código entre distintas plataformas de .NET
lang: es
github: https://github.com/fferegrino/pcls
featured_image: featured.png
tweet_id: 661730211222810624
tags: VisualStudio
featured_tag: VisualStudio
---

Imaginémonos esto:  

Supongamos que es nuestra tarea desarrollar una calculadora (que suma dos números) en C# para varias plataformas, entre ellas:
  
- Android (con Xamarin.Android) 
- Consola de Windows  
- Sitio Web  

Estas tres son plataformas radicalmente distintas, cada una tiene características que son únicas para ella y es por eso que hasta hasta hace poco tiempo, nuestra única opción para desarrollar nuestra calculadora era escribirla una vez para cada plataforma, una librería para cada uno... algo así:  

<img src="/images/que-son-portable-class-library__multiplelibs.png" title="Múltiples librerías" />
  
Como podemos ver, debíamos escribir el mismo código tres veces a pesar de ser el mismo lenguaje, y si en el futuro se nos ocurría llevar nuestra calculadora a Xbox tendríamos que reescribir otra vez nuestro código para dicha plataforma.  
  
## PCL  
Sin embargo todo cambió con la aparición de las PCL (**Portable** Class Library o Biblioteca de Clases Portable), que nos permiten compartir código entre distintas plataformas de `.NET` a través de una sola librería ("una sola dll", podríamos decir). Con ella podemos hacer algo como esto:

<img src="/images/que-son-portable-class-library__singlelib.png" title="Única librería" />

Y esto es gracias a que nuestra calculadora es bastante simple, para hacer el cálculo no necesitamos leer datos de consola, ni acceder al GPS del celular ni menos montar un servidor nuestra página.  

### Creando una PCL  
Desde Visual Studio tebemos ir a `Archivo / File` &#8594; `Nuevo / New` &#8594; `Proyecto / Project`

<img src="/images/que-son-portable-class-library__pclcreation.jpg" title="Creación de una PCL" />

Poseriormente aparecerá una pequeña ventana con varias casillas de verificación a través de las cuales debemos elegir las plataformas sobre las que queremos que nuestra librería funcione, una vez elegidas damos click en <code class="button">Aceptar</code> 

<img src="/images/que-son-portable-class-library__targetpcl.jpg" title="Targets" />

Y listo, solo queda referenciar a nuestra biblioteca desde nuestros proyectos. 


### Limitaciones  
Como podemos ver, al momento de crear una *biblioteca portable* debemos elegir sobre qué plataformas queremos que funcione y es quen es mediante esta selección estamos limitando la cantidad de características del framework .NET a las que vamos a tener acceso desde ella, en esta imagen se explica un poco mejor:

<img src="/images/que-son-portable-class-library__caracteristicas.png" title="Características entre múltiples PCL" />
  
La *biblioteca portable* siempre tomará la menor cantidad de características posibles para funcionar en todas las plataformas elegidas.

### Usos  
En el ejemplo de este post usé una calculadora (que suma únicamente dos números) pero en realidad pude haber puesto en una PCL cualquier cosa, desde la llamada a un web service hasta un cálculo matemático más complejo. Por cierto, <a href="https://github.com/fferegrino/pcls" target="_blank">el código de este post está en GitHub</a>.

Las posibilidades de uso son muchísimas, diversos paquetes de NuGet se benefician de estar hechos en PCLs permitiéndoles así poder ser instalados en varias plataformas usando un mismo ensablado.  

Entre los diversos usos que se le pueden dar a una PCL está el de contener nuestras vistas en Xamarin.Forms, en este caso, la librería se comparte entre los proyectos de Xamarin.Android, Xamarin.iOS y Windows Phone.