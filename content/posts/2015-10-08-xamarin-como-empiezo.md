---
layout: post
title: ¿Cómo empiezo con Xamarin?
date: 2015-10-08 21:00:00
author: Antonio Feregrino
categories: Xamarin
summary: ¿Quieres iniciar a desarrollar con Xamarin y no sabes por donde empezar? Acá te digo.
lang: es
alias: /xamarin-como-empiezo/index.html
featured_image: featured_true.jpg
tags: Xamarin
featured_tag: Xamarin
---

¿Quieres iniciar a desarrollar con Xamarin y no sabes por donde empezar? Acá te digo. Este post está dividido entre hardware y software cada sección con sus respectivos links y al final un pequeño FAQ. Mi recomendación es que lo leas todo y luego regreses a las secciones que apliquen para tu caso.

### Hardware  
Para empezar, la primera duda que muchos tienen es respecto al hardware. Me basaré en los requerimientos "mínimos" para desarrollar y publicar apps para las tres plataformas móviles (iOS, Android y Windows) y a pesar de que algunos de los requerimientos en hardware tienen mucho que ver con los emuladores de dispositivos, no puden pasar desapercibidos.  

#### Dispositivos
No necesitas tener el dispositivos físicos para la mayor parte de desarrollo de las apps, para ello tenemos los emuladores ;). Pero, si lo que quieres es que tu producto final tenga mejor calidad, definitivamente debes tratar de conseguir un dispositivo de la plataforma que vas a desarrollar. Nunca está de más.  

#### ¿Mac o PC?
Si quieres desarrollar para las tres plataformas, debes tener una Mac **y** una PC disponibles. Sí, las dos. Si únicamente tienes Mac, podrás desarrollar aplicaciones para Android e iOS solamente. Si solo tienes una PC, podrás desarrollar para Android y Windows Phone.  

##### Siendo más específico
 - **Procesador**: para usar el emulador de Windows Phone en una PC es necesario un procesador de 64 bits que soporte Hyper-V y para usar los emuladores de iOS en una Mac es necesario un procesador Intel. Yo iría por un i5 o más. 
 - **RAM**: No voy a mentir, los entornos de programación que se usan así como los emuladores tienden a consumir muchos recursos al momento de compilar o depurar, así que diría que necesitas al menos 4 Gb de memoria, cualquier otra cosa únicamente te provocará muchas frustraciones.  


### Software
Una vez que tenemos el hardware, es necesario hablar del software, vamos por plataformas.  

#### PC  

##### Windows
Necesitas Windows 8 Pro o superior pero **si no estás interesado en** desarrollo para **WP 8** puedes usar Windows 7 sin problema.  

##### IDE
Si estas usando una PC como dispositivo principal de desarrollo, tienes dos opciones como entorno de programación:  

- *Visual Studio* cualquier versión profesional o superior del 2010 en adelante funcionará. Y ahora también puedes utilizar la <a href="https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx" target="_blank">versión <i>Community</i></a>.  

- *Xamarin Studio para Windows*, este IDE siempre se <a href="https://xamarin.com/download" target="_blank"> instala junto con Xamarin</a> y es gratuito. Si usas este ide en Windows no podrás crear apps para Windows Phone ni iOS.

##### Otras herramientas de desarrollo
Entre las otras herramientas de desarrollo podemos encontrar los  *development kits*, para hacer apps con Xamarin, necesitamos el <a href="https://developer.android.com/sdk/index.html#Other" target="_blank">SDK de Android</a> y el <a href="https://www.microsoft.com/es-mx/download/details.aspx?id=35471" target="_blank">SDK de Windows Phone 8</a>, claro, dependiendo de las plataformas que queremos abarcar en el desarrollo.  
  
#### Mac  

##### Mac OS X  
Necesitarás Mac OS X 10.9.3+ (Mavericks), 10.10 o superior. 

##### IDE
Cuando usas Mac, no tienes opciones, necesitas la <a href="https://developer.apple.com/xcode/download/" target="_blank">última versión de <i>Xcode</i></a> **y** <a href="https://xamarin.com/download" target="_blank"><i>Xamarin Studio para Mac</i></a>, Xamarin como editor principal y Xcode para hacer uso de los emuladores, empaquetar apps y usar los editores gráficos de interfaces.  

##### Otras herramientas de desarrollo  
Al igual que con Windows, necesitarás el  <a href="https://developer.android.com/sdk/index.html#Other" target="_blank">SDK de Android</a> y el <a href="https://developer.apple.com/ios/download/" target="_blank">SDK de iOS</a>.
  
### FAQ (o algo parecido)
<dl>

<dt><b>Son muchas descargas, ¿no hay algo más sencillo?</b><dt>
<dd>Hay muchos componente que vienen agrupados en paquetes, por ejemplo: cuando descargas Xamarin este te dará la opción de instalar el SDK de Android. O cuando instalas Visual Studio este te preguntará si deseas instalar el SDK de Windows Phone. O cuando instalas Xcode, también podrás instalar el SDK de iOS.</dd>

<dt><b>No tengo Mac pero quiero desarrollar para iOS</b><dt>
<dd>No hay problema, puedes usar un servicio como <a target="_blank" href="http://www.macincloud.com">macincloud</a>. Honestamente yo no he usado, pero hay quien lo recomienda. O virtualizar Mac en Windows (¡woha! en tu cara EULA), aunque no tengo experiencia con eso.</dd>

<dt><b>Solo tengo Mac pero quiero desarrollar para Windows Phone o usar el hermoso Visual Studio</b><dt>
<dd>Siempre podrás virtualizar Windows en Mac, yo a veces lo hago. Personalmente recomiendo <a href="http://store.vmware.com/store/vmware/en_US/DisplayProductDetailsPage/ThemeID.2485600/productID.323689100" target="_blank">VMware Fusion</a> aunque Parallels también funciona bien. Pro tip: <a href="http://store.vmware.com/store/vmware/html/pbPage.AcademicLandingPage" target="_blank">VMware ofrece descuento si eres estudiante</a>.</dd>

<dt><b>¿Xamarin funciona en Hackintosh?</b><dt>
<dd>¿Lograste hacer un hackintosh? tienes mi resepto, yo no pude... Este es un terreno oscuro y pantanoso, de opiniones divididas entre que sí funciona y que no funciona. Si lo intentas, me gustaría escuchar tu experiencia.</dd>

<dt><b>¿Por qué no puedo desarrollar iOS en Xamarin Studio para Windows?</b><dt>
<dd>De acuerdo con <a href="https://bugzilla.xamarin.com/show_bug.cgi?id=29405#c3" target="_blank">este comentario</a> en el bug tracker de Xamarin, la funcionalidad es poco requerida como para implementarla. Mmmmh.</dd>

</dl>    
  
<h4>Lo que sigue</h4>    
En el siguiente post, abordaré un poco sobre las licencias de Xamarin y lo que ofrecen y en otro post más a futuro veremos que Xamarin no solo es para teléfonos celulares.