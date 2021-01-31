---
layout: post
title: Code Snippets en Visual Studio
date: 2016-02-22 10:00:00
author: Antonio Feregrino
categories: c-sharp visual-studio
summary: Agiliza el proceso de desarrollo en Visual Studio mediante el uso de fragmentos de código, que no solo te ahorraran unas cuantas tecleadas, sino te librarán de tareas repetitivas.
lang: es
featured_image: featured.png
images_folder: code-snippets/
github: https://github.com/fferegrino/fantastic-broccoli
tweet_id: 702127650437894144
tags: VisualStudio
featured_tag: VisualStudio
---

Los *code snippets*, o, **fragmentos de código**, son otra de las grandes prestaciones que nos ofrece el entorno de desarrollo Visual Studio, conocerlos y usarlos, puede ayudarnos a la hora de desarrollar. En este post hablaré de ellos: ¿qué son y cómo funcionan? ¿cuáles tiene VS instalados? y cómo crear tus propios fragmentos.

### ¿Qué son?
Pues como su nombre lo indica son fragmentos de código son "pequeñas" porciones de código que podemos reutilizar una y otra vez dentro de los archivos de código de nuestra aplicación, todo esto se hace en al momento de escribir el código y nada tiene que ver con alguna característica de C#.  

Estos fragmentos de código se cargan de un directorio cuando arranca Visual, y podemos acceder a ellos a través de un menú contextual o aprovechar una de las ventajas de los *snippets*, la cual nos permite acceder a ellos a través de una especie de "palabra reservada" o atajo y la tecla <kbd>tab</kbd>. 

#### Funcionamiento  
Su funcionamiento es sencillo, mientras estamos editando escribimos el atajo y presionamos dos veces la tecla <kbd>tab</kbd>, tras lo cual como por arte de magia aparecerá el fragmento de código completo:

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/cw.gif" title=""Console.WriteLine snippet"" />

En este caso, si revisas bien, el atajo usado fue `cw`, que podríamos interpretar como la `C` de `Console` y la `w` de `WriteLine`, el hecho de que sea así solo es una coincidencia o un gran acierto del creado, pero no es necesario que el atajo tenga que ver con el código que genera, más adelante, cuando veamos cómo crear tus propios fragmentos, verás por qué lo digo.

También hay fragmentos de código que contienen partes editables dentro de ellos, de tal manera que podamos personalizarlo con el contexto de la aplicación que estamos desarrollando. Por ejemplo, mira este:

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/prop.gif" title="Prop" />

El atajo usado fue `prop` y una vez insertado, señaló dos partes del código a las cuales accedemos presionando <kbd>tab</kbd> nuevamente, en este caso permite editar el tipo de la propiedad y su nombre, para finalizar la edición presionamos enter.

### Snippets de Visual Studio  
Apenas terminas de instalar VS puedes comenzar a usar los fragmentos que trae por default, para C# son aproximadamente unos 35.  
  
En <a href="https://msdn.microsoft.com/en-us/en-en/library/z41h7fat.aspx" rel="nofoloww" target="_blank">este enlace</a> está la lista entera, pero la siguiente es una lista de los que yo uso más:  
  
 - `#region` que podemos usar para crear una agrupación física de líneas de código en nuestro archivo

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/region.gif" title="Region directive" />

 - `ctor` que se usa para crear un constructor de la clase en la que se declara

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/ctor.gif" title="Constructor" />

 - `for` crea un ciclo for con valores editables para la variable de iteración y sobre la que se va a comprobar el ciclo  

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/for.gif" title="Constructor" />

 - `foreach` crea un ciclo "para cada", nos permite editar el tipo de dato y los nombres de los objetos

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/foreach.gif" title="Constructor" />

 - `prop` que crea una propiedad autoimplementada  
 
 - `propg` que crea una propiedad autoimplementada que únicamente define un *getter*  
 
 - `propfull` que crea una propiedad con su respectivo campo de respaldo


Si ya usas los fragmentos seguramente coincidiremos en algunos, y si no, cuando los empieces a usar, me cuentas.  

### Creando fragmentos  
¿Qué sería de un IDE si no pudieramos personalizarlo? con VS podemos crear fragmentos para usarlos e inclusive compartirlos con nuestros amigos o compañeros de trabajo. 

Primero que nada, los fragmentos se almacenan en archivos con extensión `.snippet` (que por dentro son XML). Y a pesar de que existen algunas extensiones que permiten manipularlos, para mi siempre ha sido mejor editarlos manualmente.  
  
El lugar por default en donde debes colocar tus snippets es dentro de la carpeta `\Documents\Visual Studio 20[XX]\Code Snippets\[Lenguaje]\My Code Snippets`.

Bien, ahora que sabemos esto, vamos a crear un *snippet*, descarga <a target="_blank" rel="nofollow" href="https://raw.githubusercontent.com/fferegrino/fantastic-broccoli/master/sample.snippet">este archivo</a> (clic derecho, guardar cómo), este archivo contiene una plantilla para lograr al menos algo básico, ábrelo con tue editor de texto o XML favorito, yo uso <a href="https://notepad-plus-plus.org/" target="_blank">Notepad++</a> y revisa su contenido.

Es sencillo como puedes ver, las partes esenciales son:  
 
 - `Header` en donde va el título, el autor, la descripción y el atajo.
 - `Snippet` en donde va el código que queremos que se inserte en el documento.  

Reemplaza los valores como desees, y no olvides poner tu código dentro de `<![CDATA[` y `]]>`, mira, este es el que yo he creado: 

```xml  
<CodeSnippet Format="1.0.0">
    <Header>
        <Title>I love C# snippet</Title>
        <Author>Antonio Feregrino</Author>
        <Description>Tells the world you love C#</Description>
        <Shortcut>love</Shortcut>
    </Header>
    <Snippet>
        <Code Language="CSharp">
            <![CDATA[Console.WriteLine("I love C#");]]>
        </Code>
    </Snippet>
</CodeSnippet>
```  

Es hora de copiarlo en el directorio desde el que serán cargados e iniciar Visual Studio (hasta el día de hoy sigue siendo necesario reiniciar el IDE cada vez que cambiamos un *snippet*).

Entonces al escribir en un archivo de código C# el atajo `love` y presionar <kbd>tab</kbd> <kbd>tab</kbd>, sucede esto:

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/love.gif" title=""I love C#"" />

Vamos a crear algo un poco más complejo, algo que permita reemplazar valores dentro del fragmento de código, como el caso de `prop`, para realizar la tarea puedes seguir trabajando con el archivo que tienes, o <a target="_blank" rel="nofollow" href="https://raw.githubusercontent.com/fferegrino/fantastic-broccoli/master/sample-2.snippet">descargar este otro</a>.

Lo importante es añadir un hijo llamado `Declarations` como hijo de `Snippet`. Dentro de `Declarations` es donde debemos definir los reemplazos que vamos a necesitar. Existen dos tipos de reemplazos `Literal` y `Object`, por ahora vamos a quedarnos solo con `Literal`, así que añade un elemento d este tipo dentro de `Declarations`, a su vez, `Literal` debe tener como hijos los elementos `ID` y `Default`. Suena bastante complicado... y por eso pongo un ejemplo de lo que digo:  

```xml  
<CodeSnippet Format="1.0.0">
    <Header>
        <Title>I love ?? snippet</Title>
        <Author>Antonio Feregrino</Author>
        <Description>Tells the world you love something</Description>
        <Shortcut>lovesth</Shortcut>
    </Header>
    <Snippet>
        <Declarations>
            <Literal>
                <ID>something</ID>
                <Default>C#</Default>
            </Literal>
        </Declarations>
        <Code Language="CSharp">
            <![CDATA[Console.WriteLine("I love $something$");]]>
        </Code>
    </Snippet>
</CodeSnippet>
```  
  
Como puedes ver, dentro del código del fragmento está el `ID` que puse dentro de la declaración del reemplazo, la novedad es que el ID está delimitado por `$` lo cual indica que es un valor que será reemplazado.  

<img src="https://thatcsharpguy.github.io/postimages/code-snippets/lovesth.gif" title="I love other things" />
  
##### Cambiando la ruta de los fragmentos  
Si lo que quieres es cambiar la ruta de tus fragmentos a otra carpeta dentro de tu PC, puedes acceder al Administrador de Fragmentos de Código que está en el menú Herramientas, dar clic en Añadir y seleccionar la carpeta de la que deseas que se carguen los fragmentos. En mi caso, lo tengo configurado de esa manera porque uso GitHub para sincronizar algunos de los fragmentos que uso, si quieres verlos, <a href="https://github.com/fferegrino/fantastic-broccoli" target="_blank">visita el repositorio</a>.  

### Ventajas   
La ventaja de los *snippets* va más allá del ahorro de tiempo que introduce en nuestro proceso de desarrollo, y esto lo digo porque tal vez la ventaja de escribir un if completo o usar el *snippet* no sea tan significativa. Si no el simple hecho de liberar nuestras mentes de colocar el cursor en la posición correcta o de cerrar correctamente los corchetes, es en sí, la gran ventaja de estos.

Además de que a través de los *snippets* se puede implementar un [estilo de programación](/post/importancia-de-programar-con-estilo) dentro de la organización en la que trabajamos, imagínate que todos usan los mismos fragmentos para programar, seguramente las diferencias entre el código de un programador y otro se disminuirían.  
  
### Desventajas  
(...) La única desventaja real que existe es que podríamos acostumbrarnos a usarlos y que cuando no los tengamos nos costará un poco de trabajo programar sin ellos... pero vamos, que todos los IDE que son comúnmente usados para programar en .NET tienen soporte para fragmentos de código.    