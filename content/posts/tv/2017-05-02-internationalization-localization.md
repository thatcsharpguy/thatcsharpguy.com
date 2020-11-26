---
title:  Internacionalización y localización
date:  2017-05-02 18:00:00
youtube_id: skqXI77RuvU
images_folder:  /tv/i18nl10n/
summary:  La internacionalización y localización son un par de procesos que nos ayudarán a llevar nuestras aplicaciones a más lugares del mundo.
featured_image:  featured.jpg
tags:  Meta, Tv
featured_tag:  Tv
layout: video
---

La internacionalización y localización son términos que probablemente hayas escuchado ya, sobretodo si trabajas desarrollando aplicaciones para múltiples países. 

También son términos que usualmente se confunden y se usan indistintamente, sin embargo, esto es un error:

# Internacionalización
Abreviada como "i18n" porque en inglés hay 18 letras entre la i y la n.   

Consiste en preparar una aplicación para soportar múltiples lenguajes, culturas y regiones del planeta. La preparación de una aplicación consiste en muchas cosas, pero generalmente consiste en:  

 - Cambiar la configuración de la base de datos para que acepte otro tipo de caracteres, como las tildes en el alfabeto del español o francés, hiragana japonés o el alfabeto cirílico, por poner ejemplos.   
 - Interfaces que modifiquen su tamaño o forma de acuerdo a las longitudes del texto, que al cambiar de idioma una palabra el texto de una etiqueta esta no se desborde haciendo que se vea mal o que inclusive deje de ser funcional.    
 - Separar las cadenas de texto de la interfaz como instrucciones, o textos de la interfaz, ponerlos en archivos separados para que sea fácil cambiarlos después sin tener que modificar mucho. Escribí un  <a href="..\..\post\localisation-resx" target="_blank">post sobre esto en C#</a>.
 - Separar los segmentos de información que se presenta al usuario de tal forma que se puedan crear plantillas de texto de fácil modificación, esto para nunca concatenar cadenas en código, sino usar estas plantillas con marcadores de donde debe ir cada parámetro, para los lenguajes en donde se invierte la ubicación de las palabras para hablar de cantidades, por ejemplo.
 - Incluir los signos de puntuación dentro de estas cadenas de texto puesto que no todos los lenguajes los usan de la misma manera.  
 - Habilitar los campos de entrada para que acepten fechas, cantidades y números de acuerdo a la cultura, lenguaje y región  del usuario.  
 - Permitir que los usuarios escriban su nombre COMO QUIERAN, si su nombre tiene un guion, un acento al revés (?), doble comillas… lo que sea, es SU NOMBRE. También recuerda que no todos los nombres son iguales, en México tenemos usualmente uno o dos nombres y dos apellidos, mientras que en otras partes es normal tener un nombre y un solo apellido, mientras que en otros lo normal es tener un nombre único y un  matronímico o un patronímico.   
 
La internacionalización es un proceso que, si se lleva a cabo de manera correcta, únicamente se debe hacer una vez.  

# Regionalización o Localización 
Abreviado l10n, de nuevo, por las 10 letras que hay entre la l y la n.  

Pero bueno, no podemos hablar de localización sin antes saber qué es un *locale*.

## Locale o la configuración regional  
Es un conjunto de parámetros que identifican el lenguaje, la región y la codificación de caracteres con los cuales puede funcionar una aplicación.  

Esto se debe a que un mismo lenguaje puede ser usado en diferentes países, un mismo país puede tener varios lenguajes y no solo eso, sino que a pesar de que un mismo lenguaje se hable en dos países, puede que existan otras diferencias como la forma en la que se escriben las fechas, los montos de dinero, los números telefónicos, algunas palabras, etcétera. 

Un Locale está compuesto de alguna variación de la siguiente estructura: `language[_territory][.codeset][@modifier]`, en las cuales tanto el territorio, la codificación y el modificador son totalmente opcionales.  

Por ejemplo, el *locale* para México con la codificación `UTF-8` es el siguiente:  

`es_MX.UTF-8` 

Mientras que un *locale* para el idioma francés es simplemente:  

`fr`   

Cada plataforma, dispositivo, lenguaje puede soportar distintos locales, pero en general todos soportan los mismos, y muchos se adecúan a un estándar. 

## La localización    

La localización significa básicamente adaptar la presentación de una aplicación al lenguaje, región y codificación del usuario que está trabajando en nuestra app.  

Este proceso de adaptación va más allá de una simple traducción, ya que no solo son las partes textuales de una aplicación, sino que se extiende al contenido multimedia, sonidos imágenes, colores, animaciones e inclusive pólizas de privacidad

Y así mismo, la localización a veces no se detiene en elementos propios de la aplicación como los botones, los menus, los íconos o las instrucciones en los formularios. Sino que también se puede extender a otros elementos como la documentación de la app si ofreces una API para ser consumida por terceros.  

Para llevar a cabo el proceso de localización se sugiere contar con la ayuda de expertos en la materia, sobretodo expertos de la región para la cual se va a localizar tu producto, esto para que no te vayas a llevar una sorpresa al momento de que tus usuarios comiencen a usar el producto en su idioma.

¿Han hecho algún proyecto que requiera de distintos lenguajes? ¿conocen qué mecanismos les ofrece su plataforma de desarrollo para llevar a cabo esta tarea?
