---
layout: post
title: Conoce la versión de tu app con Xamarin.Forms
date: 2017-04-11 19:00:01
author: Antonio Feregrino
summary: El asignarle un código de versión a cada una de las actualizaciones de tu app es de vital importancia, te ayudará a tener un punto de referencia cuando tengas que resolver algún bug o recolectar información para analizar el desempeño de tus apps.
featured_image: featured.jpg
images_folder: /nugets/versioning/
github: https://github.com/ThatCSharpGuy/VersionTrackingSample
lang: es
tags: NuGetRecomendado, XamarinForms, Xamarin
featured_tag: NuGetRecomendado
---

Y tu, ¿cómo versionas tus apps? El asignarle una versión a cada publicación de una app suele ser muy importante para cuando tenemos que resolver algún bug y le solicitamos directamente al usuario que nos indique qué versión de la app está ejecutando. También resulta útil si llevamos a cabo alguna especie de análisis de qué versiones de nuestras apps son más utilizadas e inclusive poder indicarle al usuario que la versión de la app que está ejecutando no es la más reciente. 

Android e iOS hacen muy sencilla la tarea de asignarle una versión a cada publicación que hacemos de alguna aplicación:   

## Android  
En Android existen dos valores que nos sirven para establecer la versión de nuestra app, uno es público y visible para el usuario mientras que el otro es privado y disponible únicamente para nosotros y la tienda de aplicaciones.  

Estos dos valores son: 

 - `android:versionCode` es el valor "privado". El propósito de este número es el de indicar al desarrollador (y a la tienda) que una versión de una app es más reciente que otra. Este valor es siempre un entero, así que puedes usar cualquier sistema de versionamiento que desees, siempre y cuando al momento de publicar una actualización de tu app, el valor del nuevo `apk` sea mayor al que ya existía en la tienda. Toma en cuenta que el valor máximo es 2,100,000,000; así que si pensabas tener dos mil millones de actualizaciones de tu app... tal vez Android no es para ti.  

 - `android:versionName` es el valor que los usuarios ven en la tienda de aplicaciones, en teoría puede ser cualquier cadena de caracteres... aunque no es así. Lo recomendable es usar números y `.`, combinados en una nomenclatura clara que le indique a tus usuarios a simple vista qué versión de la app están descargando y ejecutando. No tiene ningún otro propósito más que el de ser mostrado, así que no tiene otra restricción.

Para modificarlos es necesario acceder al archivo `AndroidManifest.xml`. En Visual Studio o Xamarin Studio te podrás beneficiar de contar con un editor gráfico:  

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/androidmanifest.jpg" title="AndroidManifest GUI editor" />

## iOS  
Para iOS la cosa no cambia mucho, igualmente existen dos valores con las mismas características (uno público y el otro privado) pero con distinto nombre:  

 - `CFBundleVersion` es el valor "privado", cumple el mismo propósito que el `versionCode` de Android, pero el valor de este es una cadena. Usualmente son tres números enteros positivos separados por un punto, toma en cuenta que cada vez que cargues una nueva actualización de tu app a la tienda, el valor del nuevo paquete debe ser mayor al que ya existía. Por ejemplo, si tienes un paquete con un valor de `1.2.3` no podrás cargar uno con `1.2.2` pero si uno `1.3.0`.

 - `CFBundleShortVersionString` es la versión que es pública para el usuario, en teoría es una versión corta de `CFBundleVersion` aunque la documentación indica que puede ser cualquier cadena con el mismo formato que la anterior. Toma en cuenta que nuevamente, este valor debe ser algo que el usuario pueda entender fácilmente, pues serán ellos quienes lo verán más.  

Estos valores se almacenan en el archivo Info.plist, para el cual también tenemos disponible un editor gráfico:  

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/infoplist.jpg" title=""Info.plist GUI editor"" />

## Version Tracking Plugin
Este pequeño paquetito de NuGet nos ayuda a acceder programáticamente a estos dos números directamente desde nuestro proyecto de Forms. 

Con el *Version Tracking Plugin* tenemos acceso al historial de instalaciones y actualizaciones de nuestra app en el dispositivo del usuario. Es simple de usar, una vez instalados los paquetes en los tres proyectos tenemos que inicializarlo en el método `OnCreate` de la `MainActivity` y en el método `FinishedLaunching` del `AppDelegate`, bastará con llamar al siguiente método:  

```csharp  
CrossVersionTracking.Current.Track();
```  

Una vez hecho esto, a través de la propiedad `CrossVersionTracking.Current` tendremos acceso a todas sus propiedades:   

### La primera vez  
Saber si es la primera vez que el usuario abre nuestra app con `IsFirstLaunchEver` nos puede ayudar para darle la bienvenida, saber si es la primera vez que está ejecutando una actualización con `IsFirstLaunchForVersion` nos puede ayudar para mostrarle las nuevas características añadidas en la app. Y saber si es la primera vez que está ejecutando una nueva compilación con `IsFirstLaunchForBuild` puede ayudarnos con los *analytics* de nuestra app.  

### Trabajando con versiones y *builds* específicas  
Muchas veces no nos bastará con saber si es la primera vez que una versión se está ejecutando, también debemos saber exactamente cuál es la versión, para eso podemos usar la propiedad `CurrentVersion` o si por alguna razón queremos conocer la versión que estaba previamente instalada también podemos usar `PreviousVersion` y para ir aún más atrás en la historia podemos usar `FirstInstalledVersion` y si por alguna razón queremos obtener más información, podemos usar `VersionHistory` para acceder a un historial completo de las versiones que se han instalado en el dispositivo. Para acceder a las *builds* tenemos un conjunto de propiedades similares.  

### Eventos  
Con este *plugin* también podemos establecer eventos que se ejecuten cuando una versión o *build* es ejecutada por primera vez. Podemos usar `OnFirstLaunchOfBuild` y `OnFirstLaunchOfVersion` para este propósito, aunque en mi opinión, estos no son tan útiles.

## Un Ejemplo
He montado un ejemplo sencillo, que puedes obtener en GitHub, con unas cuantas `Labels` y un par de `ListView` para mostrar los datos que se pueden recuperar con este NuGet:  

#### Primera versión  
Tenemos la primera versión de la app, con los siguientes valores en el nombre de la versión y la *build*:  

<div class="pure-g">
    <div class="pure-u-1-2">
```xml  
<!-- Android -->
android:versionCode="1" 
android:versionName="1.0"  
 
 
```  
	</div>
    <div class="pure-u-1-2">
```xml  
<!-- iOS -->
<key>CFBundleShortVersionString</key>
<string>1.0</string>
<key>CFBundleVersion</key>
<string>1.0</string>
```  
	</div>
</div>

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/one.jpg" title="Version one" />

#### Cambio en el *build*   

<div class="pure-g">
    <div class="pure-u-1-2">
```xml  
<!-- Android -->
android:versionCode="2" 
android:versionName="1.0"  
 
 
```  
	</div>
    <div class="pure-u-1-2">
```xml  
<!-- iOS -->
<key>CFBundleShortVersionString</key>
<string>1.0</string>
<key>CFBundleVersion</key>
<string>1.1</string>
```  
	</div>
</div>

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/two.jpg" title="Build bump" />

#### Cambio en la versión     

<div class="pure-g">
    <div class="pure-u-1-2">
```xml  
<!-- Android -->
android:versionCode="2" 
android:versionName="1.2"  
 
 
```  
	</div>
    <div class="pure-u-1-2">
```xml  
<!-- iOS -->
<key>CFBundleShortVersionString</key>
<string>1.2</string>
<key>CFBundleVersion</key>
<string>1.1</string>
```  
	</div>
</div>

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/three.jpg" title="Version bump" />

#### Cambio en la versión y *build*  

<div class="pure-g">
    <div class="pure-u-1-2">
```xml  
<!-- Android -->
android:versionCode="2" 
android:versionName="2.0"  
 
 
```  
	</div>
    <div class="pure-u-1-2">
```xml  
<!-- iOS -->
<key>CFBundleShortVersionString</key>
<string>2.0</string>
<key>CFBundleVersion</key>
<string>2.0</string>
```  
	</div>
</div>

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/four.jpg" title="Bumped both" />

#### Cambio en la versión y *build*  

<div class="pure-g">
    <div class="pure-u-1-2">
```xml  
<!-- Android -->
android:versionCode="3" 
android:versionName="3.0"  
 
 
```  
	</div>
    <div class="pure-u-1-2">
```xml  
<!-- iOS -->
<key>CFBundleShortVersionString</key>
<string>3.0</string>
<key>CFBundleVersion</key>
<string>3.0</string>
```  
	</div>
</div>

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/five.jpg" title="Bumped both" />

#### Si el usuario borra la app...
Desde luego, toda esta información se pierde si el usuario borra la app. En este caso, el usuario borró y reinstaló la versión tres, y tras abrirla nuevamente, esta es la pantalla que aparece:  

<img src="https://thatcsharpguy.github.io/postimages/nugets/versioning/six.jpg" title="Fresh versioning" />

## Instalación  
Para instalarlo basta con instalar el <a href="https://www.nuget.org/packages/Plugin.VersionTracking/" target="_blank">paquete de NuGet</a> Plugin.VersionTracking.

```  
PM> Install-Package Plugin.VersionTracking
```  

Recuerda que debes instalarlo tanto en los proyectos cliente como en el proyecto de Forms.  

Si deseas ver el código que le da a este *plugin* su poder, puedes visitar el <a href="https://github.com/colbylwilliams/VersionTrackingPlugin" target="_blank">proyecto en GitHub</a>, para que colabores con el creador si es que le encuentras algún error.  