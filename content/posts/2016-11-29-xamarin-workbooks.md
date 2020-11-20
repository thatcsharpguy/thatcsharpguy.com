---
layout: post
title: Bienvenido a los Xamarin Workbooks
date: 2016-11-29 19:00:01
author: Antonio Feregrino
summary: Los Workbooks son una especie de documentos &quot;vivos&quot; con los que puedes compilar y ejecutar código en C#.
featured_image: featured.jpg
images_folder: /xamarin/workbooks/
github: https://github.com/ThatCSharpGuy/guoerbucs/blob/master/hola.workbook/index.workbook
lang: es
tweet_id: 803747267261960192
tags: AprendeCSharp, Xamarin
featured_tag: AprendeCSharp
---

Los Workbooks son una especie de documentos "vivos" con los que puedes compilar y ejecutar código en C#. Con ellos, aprender a programar a .NET cobra un nuevo significado. Mira, por ejemplo, cómo se ve un “Hola mundo“ en ellos:

```csharp  
Console.WriteLine("Hola mundo");
```  

### Interfaz
Vamos a ver algunos elementos de la interfaz antes de adentrarnos de lleno en el tema. Estos son los controles que te encontrarás muy seguido:

![](http://i.imgur.com/LujKEPN.png "Interfaz")

Con el símbolo de `+` puedes agregar un nuevo bloque de código, con el de `”` puedes agregar un bloque de texto y con el de `✖️` puedes eliminar el bloque seleccionado.

Otro de los elementos con los que seguramente estarás muy familiarizado es el *Intellisense*:

![](http://i.imgur.com/V6LUPYh.png "Intellisense")

### Declaración de variables

También podemos declarar variables y asignarlas para el documento:

```csharp  
int entero; // declaración
float flotante = 10f; // Asignación
decimal x = (decimal)flotante * 3;
decimal y = x / 6;
```  

### Declaración de clases

Inclusive podemos crear clases completas

```csharp  
public class Pokemon
{
  public int Id { get; set; }
  public string Name { get; set; }
  public int Height { get; set; }
  public int Weight { get; set; }
}
```  

Y luego, instanciarlas:

```csharp  
var p1 = new Pokemon();
p1.Name = "Pikachu";
```  

### NuGet

Oh, y también puedes insertar paquetes de *NuGet*, para hacerlo, tienes que ir a la barra de menús `Archivo > Agregar paquetes` y listo. Por ejemplo, en este Workbook agregué los paquetes de las librerías HTTP de Microsoft y Newtonsoft.Json para manipular este formato. Vamos a consultar la PokéAPI

Una vez agregado el paquete aparecerán unas directivas para preprocesador que le indican al Wokbook que haremos uso de dichos paquetes:

```csharp  
#r "System.Net.Http.Primitives"
#r "System.Net.Http.Extensions"
#r "Newtonsoft.Json"
```  

A partir de entonces podemos hacer uso de estas, ¡pero hey! no olvides los `using`

```csharp  
using Newtonsoft;
using System.Net.Http;
using System.Net.Http.Headers;
```  

Ahora sí, vamos a consultar la PokéAPI. Configuramos el cliente:

```csharp  
var client = new HttpClient();
client.BaseAddress = new Uri("http://pokeapi.co/api/v2/");
client.DefaultRequestHeaders.Accept.Clear();
client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
```  

Y realizamos las llamadas. ¿por qué no buscas a tu Pokémon favorito modificando la variable `pkmn`? (no olvides presionar ctrl o cmd \+ ↩︎ para ejecutar el código:

```csharp  
var pkmn = "jirachi";
var response = await client.GetAsync("pokemon/" + pkmn);
```  

¡Oh! lo olvidaba, ¿ves eso que sale debajo de nuestro código cada vez que lo ejecutamos? no es nada más que otra de las maravillas de los Workbooks, que realiza la evaluación de la última expresión y la presenta en pantalla, además de que nos da varias opciones para visualizar la información:

Ahora sí, de vuelta al código:

```csharp  
if(response.IsSuccessStatusCode)
{ // ... Read the string.
	    string result = await response.Content.ReadAsStringAsync();
        var poke = await Newtonsoft.Json.JsonConvert.DeserializeObjectAsync<Pokemon>(result);
	    Console.WriteLine($"{poke.Id:000} {poke.Name}: H: {poke.Height} and W: {poke.Weight}");
}
```  

### ¿Cómo son los Workbooks?

Por si mismos, los Workbooks no son más que archivos te texto plano (un híbrido entre YAML, Markdown y otra cosa) que son interpretados por la aplicación de escritorio.

Vamos a revisar precisamente este mismo archivo. Estas son las primeras líneas:

![](http://i.imgur.com/1xb3xPm.png "Primeras líneas de un Workbook")

La primera es un *Uniform Type Identifier*, la segunda es la especificación de para qué plataformas es nuestro espacio de trabajo (en este caso es la consola) y por último la declaración de los paquetes de los que se hace uso.

Otra cosa que podemos revisar es la forma en la que los bloques de código se especifican, es muy parecido a cuando se declaran en markdown, con la diferencia de que tiene un `csharp` después de las tildes invertidas:

![](http://i.imgur.com/ac1OWU9.png "Bloque de código C#")

Lo cierto es que varias veces le tuve que meter las manos al código fuente del archivo para lograr algunas cosas en este archivo, pero vamos, que apenas es la primera versión “estable“ de esta herramienta.

## Instalación

Si ya estás ansioso por descargar y probar los Workbooks puedes dirigirte a <a href="https://developer.xamarin.com/guides/cross-platform/workbooks/install" target="_blank">este enlace</a>.

## Usos

Seguramente ya estarás pensando en algún uso para esta nueva herramienta. El más claro que yo tengo en mente es el poder los posts de <a href="http://thatcsharpguy.com/tag/AprendeCSharp/" target="_blank">#AprendeCSharp</a>, es más, ¿por qué no comienzas por <a href="https://github.com/ThatCSharpGuy/guoerbucs/blob/master/hola.workbook/index.workbook"  target="_blank">descargar este</a> o <a href="https://developer.xamarin.com/guides/cross-platform/workbooks/samples/" target="_blank">los que Xamarin preparó para ti</a>.

Eso es todo por este post, espero descarguen los Xamarin Workbooks y los usen como una herramienta para aprender.