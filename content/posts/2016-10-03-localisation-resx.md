---
layout: post
title: Archivos de recursos y apps multilenguaje en C#
date: 2016-10-03 19:00:01
author: Antonio Feregrino
excerpt: Como desarrolladores, tendemos a pensar en nuestras aplicaciones como funcionales para una región o lenguaje específicos, sin preocuparnos sobre traducir e internacionalizarlas, preparándolas así para ser accesibles aun número mayor de usuarios.
featured_image: featured.jpg
images_folder: /aprende-c-sharp/resx/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Resxs
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Es muy común que siempre que desarrollamos aplicaciones, nosotros como desarrolladores lo hagamos pensando en un solo lenguaje... pero ¿qué pasará más adelante cuando nuestra app se vuelva popular y gente de diversas partes del mundo la comience a usar? para nuestra suerte, el framework de .NET nos ofrece una solución bastante sencilla para este problema: los archivos de recursos `resx`.

Los archivos de recursos, o "resx" nos permiten justamente lograr la tarea de localización de una aplicación. Estos archivos no son nada más que archivos XML en los que se almacenan datos en la forma "clave-valor(-descripción)", es importante señalar que en estos archivos se pueden almacenar diversos tipos de dato, pero nosotros únicamente usaremos cadenas.  

Primero que nada, lo ideal es tener ya definidas todas las cadenas que usaremos en la app, aunque no pasa nada si las vas descubriendo conforme desarrollas. Para nuestra mini aplicación usaremos las siguientes:  

<table>
<tr>
<th>Llave</th>
<th>Descripción</th>
<th>Valor por default</th>
</tr>
<tr>
<td>ChangeLanguage</td>
<td>El texto que señala al usuario que puede cambiar el lenguaje</td>
<td>Change language</td>
</tr>
<tr>
<td>CurrentLanguage</td>
<td>Texto que debe ser formateado para indicar cuál es el lenguaje actual de la aplicación</td>
<td>Current language {0}</td>
</tr>
<tr>
<td>Exit</td>
<td>Texto que indica el punto de salida</td>
<td>Exit</td>
</tr>
<tr>
<td>Hello</td>
<td>Texto de bienvenida al usuario</td>
<td>Hello!</td>
</tr>
</table>  

### El archivo .resx

Para comenzar a usar los archivos (y especificar los valores por default) debemos agregar un archivo de recursos, esto, en Visual Studio se realiza dando click contextual dentro del proyecto al que lo queremos agregar, después seleccionando "Nuevo elemento" y seleccionando "Archivo de recursos":

<img src="/images/aprende-c-sharp__resx__adding.png" title=""Agregando"" />

Para el caso de nuestras cadenas por default, yo elegí el nombre `Strings.resx` para el archivo, es importante que tomes en cuenta que para agregar más lenguajes los archivos deben tener el mismo nombre con una pequeña modificación que veremos más adelante.  

Una vez creado el nuevo elemento, debemos tener dos archivos relacionados entre sí: uno con terminación `.resx` y otro `.Designer.cs`, este último es un archivo generado automáticamente por el IDE que nos permitirá acceder en tiempo de ejecución a los recursos definidos dentro del `resx`:  

<img src="/images/aprende-c-sharp__resx__assoc.png" title=""Ficheros asociados"" />

Ahora, si abrimos el archivo `resx` recién creado, en Visual Studio veríamos algo como esto (en este caso ya añadí los valores):

<img src="/images/aprende-c-sharp__resx__resx-editor.png" title=""Editor de Visual Studio"" />

Este editor es especial en VS, si tratamos de abrir este mismo archivo en Xamarin Studio veremos el código fuente del archivo, algo así:

<img src="/images/aprende-c-sharp__resx__source.png" title=""Source"" />

### Añadiendo más idiomas

Para continuar añadiendo idiomas a nuestra aplicación podemos agregar nuevos archivos de recursos, recuerda que debes usar el mismo nombre que que en el primer archivo que creaste, solo que ahora debes añadir el sufijo correspondiente al idioma que deseas registrar. El sufijo se compone de dos partes, una relacionada con el idioma (*es*, *it*, *en*, ...) y otra relacionada con la región (*ES*, *MX*, *GB*, *IT*, ...). La lista completa de valores posibles la puedes consultar <a href="https://msdn.microsoft.com/en-us/library/ee825488(v=cs.20).aspx" target="_blank">en este enlace</a>.  

Para el caso de este ejemplo agregaremos los lenguajes ruso de Rusia ("ru-RU"), español de México ("es-MX"), italiano de Italia ("it-IT"), y por default, inglés:

<img src="/images/aprende-c-sharp__resx__resource-capt.png" title=""Archivos"" />

Procura utilizar las mismas llaves en todos los archivos, es importante para que todo funcione correctamente:

<img src="/images/aprende-c-sharp__resx__resource-capt-2.png" title=""Archivos dos"" />

### Uso en el código  

Y bueno, después de tanta ceremonia, podemos usar nuestras cadenas multilenguaje en el código:  

```csharp  
Console.WriteLine(String.Format(Resources.Strings.CurrentLanguage,
    currentThread.CurrentUICulture.ToString()));
Console.WriteLine(Resources.Strings.Hello);

// ... 

Console.WriteLine(Resources.Strings.ChangeLanguage);
```  

Si ejecutas el programa es muy probable que veas los textos en inglés, pero eso es fácil de cambiar.

### Cambiando el idioma con Thread.CurrentUICulture
Para cambiar el idioma de la interfaz de nuestras aplicaciones, es muy común utilizar la propiedad `CurrentUICulture` del hilo principal (`Thread.CurrentThread`), esta propiedad es del tipo `CultureInfo`. `CultureInfo` puede ser creada a partir de una cadena de texto en donde se especifique el idioma y la región de que se requiera, por ejemplo, para cambiar el idioma de la aplicación a con español de México:  

```csharp  
var language = new CultureInfo("es-MX");
Thread.CurrentThread.CurrentUICulture = language;
```  

Nuevamente, la lista completa de valores idioma-región posibles la puedes consultar en  <a href="https://msdn.microsoft.com/en-us/library/ee825488(v=cs.20).aspx" target="_blank">este enlace</a>.  

#### CurrentCulture y CurrentUICulture  
La clase `Thread` tiene también una propiedad llamada `CurrentCulture`, que es parecida a la que usamos para establecer el idioma de la app, solo que esta está relacionada directamente con los formatos de fechas, monedas, números decimales... puedes leer un poco más sobre esta diferencia en <a href="http://archives.miloush.net/michkap/archive/2007/01/11/1449754.html" target="_blank">este artículo</a>.

### Código de ejemplo  
Nunca me cansaré de repetirlo: no hay mejor forma de aprender que practicando. Es por eso que te invito a bajar el <a href="https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/Resxs" target="_blank">código de ejemplo</a> y juegues un poco con él. Si lo descargas y ejecutas, verás algo como esto:

<img src="/images/aprende-c-sharp__resx__sample.png" title=""Código de ejemplo"" />

### Para terminar  
Agregar soporte para diferentes idiomas y culturas a nuestra aplicación no es una tarea tan complicada como podría parecer, ya que, en algunos casos, si la plataforma para la que estamos desarrollando tiene soporte para estos archivos *resx*, estamos a unos cuantos clicks de distancia de que nuestra aplicación sea internacional. Y aun cuando no pienses que tu app va a ser popular, usar archivos de recursos contribuye al reuso de código, así como a evitar las llamadas "cadenas mágicas".
