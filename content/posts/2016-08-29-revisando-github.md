layout: post
title: Revisando mis repos en GitHub
date: 2016-08-29 19:00:01
author: Antonio Feregrino
excerpt: Un post un poco diferente, en este no hablaré sobre alguna característica de C# o un paquete de NuGet, sino de proyectos, pruebas y aplicaciones en los que lo he usado. Conoce algunas de las cosas que he hecho y subido a GitHub.
featured_image: featured.png
images_folder: /mis-repos-github/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/events
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp


Este fin de semana fue de limpieza, y también le tocó a algunos de mis repositorios en GitHub. Desde que descubrí esta herramienta de colaboración (basada en un sistema de <a href="../../tv/">control-versiones</a>) en Junio de 2012, la he usado tanto para tareas, mi proyecto de titulación y proyectos personales. He llegado a subir de todo un poco, y pensé que en este post sería buena idea compartir algunas de las cosas que he ido subiendo con el tiempo.  

## aprende-c-sharp
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__aprende-c-sharp.png" title=""Aprende C#"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
No hay mucho qué decir sobre este repo, en él se encuentra tódo el código de los posts con la etiqueta <a href="../../tag/AprendeCSharp">#AprendeCSharp</a>, en los cuales trato de explicar un poco más a detalle algunas de las características de este magnífico lenguaje. Muchos de los ejemplos son un simple archivo, pero decidí incluirlos en proyectos de Visual Studio para que pudieras ejecutarlos en un entorno conocido. 
<div class="links">
<a href="https://github.com/ThatCSharpGuy/aprende-c-sharp">GitHub</a> | <a href="../../tag/AprendeCSharp">Post en TC#G</a>
</div>
</div>  
</div>
<br />

## xocr
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__xocr.jpg" title=""Xocr"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
El repo <i>xocr</i> es el hogar de una de una prueba de concepto sobre cómo usar <a href="https://github.com/tesseract-ocr" target="_blank">Tesseract OCR</a>, una herramienta de reconocimiento de caracteres, con Xamarin.Forms. Originalmente realicé esta prueba como parte del desarrollo de una app en la cuál era necesario "leer" unos dígitos en un documento impreso. Funcionó de maravilla... aunque eso sí, la app quedó un poco pesada.
<div class="links">
<a href="https://github.com/ThatCSharpGuy/xocr">GitHub</a> | <a href="../tesseract-ocr-xamarin">Post en TC#G</a>
</div>
</div>  
</div>
<br />

## WhoBrokeIt
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__whobrokeit.png" title=""WhoBrokeIt"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
<i>WhoBrokeIt</i> es una app nacida del ocio, pero que a la vez puede resultar útil. Resulta que en el trabajo comenzamos a experimentar con la <i>Integración Continua</i> usando <i>Visual Studio Team Services</i>, entonces, para estar al tanto del estatus de las compilaciones desarrollé esta simple app con <a href="../../xamarin-forms">Xamarin.Forms</a>, desde la cual tienes acceso a toda la lista de tus proyectos y sus compilaciones, así puedes saber cuándo y quién subió el cambio que rompió la compilación.
<div class="links">
<a href="https://github.com/fferegrino/WhoBrokeIt">GitHub</a>
</div>  
</div>
</div>
<br />

## questacion
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__questacion.png" title=""questacion"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
Resulta que soy muy fan de Pokémon GO, pero solo lo uso en el transporte público, pero a veces, voy tan entrado en visitar todas las Pokeparadas que ni me doy cuenta en qué estación voy (y a veces no me bajo en la adecuada), entonces me decidí a programar esta pequeña app que me envía una notificación cada vez que estoy cerca de una estación de metrobús, así me entero de en donde estoy sin levantar la vista de la pantalla. Una peculiaridad de esta app es que... ¡está hecha en Java!
<br />
<br />
Dado que usé <a href="http://www.gaia-gis.it/gaia-sins/" target="_blank">SpatiaLite</a>, una extensión a SQLite para consultas geoespaciales, no me fue posible hacerla tan rápido con C# y Xamarin, es por eso que usé Java. 
<div class="links">
<a href="https://github.com/fferegrino/questacion">GitHub</a>
</div>  
</div>
</div>
<br />

## appod
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__appod.png" title=""appod"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
Creo que hacer una app de la <i>Astronomy Picture Of the Day</i> es como el hola mundo de la programación de aplicaciones, ¿así que por qué no iba a hacer una yo? para esta fue necesario el acceso a las <a href="https://api.nasa.gov" target="_blank">APIs de la NASA</a>, además de Xamarin.iOS y Xamarin.Android. Con la app de Android no tuve mayor problema, pero con la de iOS tuve que aprender un poco de AutoLayout y aún así no quedó como yo deseaba.
<div class="links">
<a href="https://github.com/fferegrino/appod">GitHub</a>
</div>  
</div>
</div>
<br />

## MrkViewer
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__MrkViewer.png" title=""Xocr"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
La mayoría de las apps que hago y que subo a GitHub tienen que ver con ideas que se me ocurren de pronto. Y esta es una de esas: quería un lector de Markdown para mi vieja Surface RT y ninguna me satisfacía, así que me puse manos a la obra. Para esta hice un control personalizado, derivado de un <code>WebView</code> y usando <a href="https://github.com/Knagis/CommonMark.NET" target="_blank">CommonMark.NET</a> para realizar la conversión de <i>md</i> a algo atractivo al usuario.
<div class="links">
<a href="https://github.com/ThatCSharpGuy/MrkViewer">GitHub</a> | <a href="../markdownview-xamarin-forms-control">Post en TC#G</a>
</div>  
</div>
</div>
<br />

## CharpHat
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__CharpHat.png" title=""Xocr"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
CharpHat fue una de las primeras cosas que intenté hacer con Xamarin.Forms, consistía en un gran reto, porque es necesario acceder a la cámara del dispositivo independientemente de la plataforma. La app en funcionalidad es sencilla: tomas una foto y te puedes poner encima un birrete de C#, como para demostrar lo mucho que te gusta el lenguaje. Lo único malo es que a pesar de estar hecha con forms, únicamente la pude hacer funcionar para Android.
<div class="links">
<a href="https://github.com/fferegrino/CharpHat">GitHub</a> | <a href="../charphat-android">Post en TC#G</a>
</div>  
</div>
</div>
<br />

## Xamarin.WindowView
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__windowView.gif" title=""WindowView"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
Xamarin.WindowView no es una app sino una herramienta para hacerlas: es una librería que contiene un control para Android. WindowView no es más que la versión en C# de <a href="https://github.com/justasm/WindowView" target="_blank">WindowView de Justas Medeisis</a>. Básicamente este control es un ImageView que te permite mover la imagen que contiene con los sensores de movimiento de tu celular, justo como en la imagen de al lado.
<br />
<a href="https://github.com/messier16/Xamarin.WindowView">GitHub</a>
</div>  
</div>
</div>
<br />

## Forms.Controls
<div class="pure-g project">
<div class="pure-u-1 pure-u-md-1-5">
<img src="/images/mis-repos-github__m16.png" title=""Xocr"" />
</div>
<div class="pure-u-1 pure-u-md-4-5">
Hay algunos controles que no tenemos disponibles cuando usamos Xamarin.Forms, es por eso que decidí hacer una librería con controles que ocupé para algunas apps. Básicamente las implementaciones para iOS están completas y 100% funcionales, las otras plataformas han sido dejadas del lado. Esto motivado a que el desarrollo de dichas apps está centrado (por el momento) solo en apps para dispositivos de Apple. Espero en el futuro poder dedicarle tiempo al resto de los sistemas operativos. 
<div class="links">
<a href="https://github.com/messier16/Forms.Controls">GitHub</a>
</div>  
</div>
</div>
<br />

Estos son solo una especie de vista previa, y mientras que algunos tienen ya su respectivo post en el blog, otros no. Con el tiempo espero poder hablar más sobre ellos y explicar parte del código, pero si estás interesado en alguno, no dudes en decirme y darle prioridad o contarte directamente cómo es que funciona.