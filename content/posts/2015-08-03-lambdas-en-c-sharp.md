---
layout: post
title: Lambdas en C#
date: 2015-08-3 18:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: "Una vez que conocemos lo que es un delegado, y lo que son Func y Action, es momento de llegar a un tema que se convierte en algo muy sencillo, además es de mis cosas favoritas en C#: Las expresiones lambda."
ideone: http://ideone.com/fork/7dfMEC
featured_image: featured.png
alias: /lambdas-en-c-sharp/index.html
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Una vez que conocemos lo que es un delegado, y lo que son Func y Action, es momento de llegar a un tema que se convierte en algo muy sencillo, además es de mis cosas favoritas en C#: Las **expresiones lambda**. Comenzaré por dar un ejemplo de su uso: 
  
Es decir, suponiendo que tenemos el método del ejemplo pasado.
```csharp  
void RealizaOperacionSecreta(List<string> palabras, Func<string, bool> filtro)
```  
Para el que escribimos un método para usarlo como filtro:
```csharp  
public static bool SoloConU(string s)
{
	return s.StartsWith("u");
}

// ...
// Ejemplo de uso:
RealizaOperacionSecrete(palabras, SoloConU);
```  
En realidad, no es necesario escribir tanto, el método ```SoloConU```, puede ser reemplazado en el código por:  
```csharp  
RealizaOperacionSecrete(palabras, (s) => { return s.StartsWith("u"); });

// O simplemente por:
RealizaOperacionSecrete(palabras, s => s.StartsWith("u"));
```  
Por definición, las expresiones lambda son "métodos anónimos para crear tipos delegados y árboles de expresión", nos enfocaremos en lo primero. El método <code>SoloConU</code> es usado solamente una vez en nuestro programa y es por eso que es un buen candidato a ser reemplazado por un método anónimo (entre otras cosas, sin nombre). Y es que podría muchas veces resultarnos bastante engorroso tener que escribir le método completo para hacer referencia a él solamente una vez dentro de todo nuestro código.

#### Crear una expresión lambda  
Me gusta imaginar la creación de expresiones lambda como una especie de recorte de los métodos tradicionales, y es que lo podemos ver como una serie de pasos que funcionan tanto para métodos con tipo de dato de retorno o para los que no lo tienen:  

<ol>
	<li>Quitamos todo lo que esté antes del primer paréntesis de apertura</li>
	<li>Agregamos el operador <code>=&gt;</code></li>
	<li>... listo</li>
</ol>  

#### Consideraciones extras  
Existen otras consideraciones que podemos tomar en cuenta si queremos reducir aún más la cantidad de código escrito. Por ejemplo si a la expresión lambda recibe **un solo parámetro** se pueden omitir los paréntesis:

<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
// Versión normal
(a) => { return a.ToString(); }
```  
	</div>
    <div class="pure-u-1-2">
```csharp  
// Versión "simplificada"
a => { return a.ToString(); }
```  
	</div>
</div>
Si la expresión lambda consta de una **única sentencia**, y esta es una **operación de retorno**, podemos omitir los corchetes, la palabra clave ```return``` y el ```;``` de fin de sentencia: 

<div class="pure-g">
    <div class="pure-u-1-2">
```csharp  
a => { return a.ToString(); }
```  
	</div>
    <div class="pure-u-1-2">
```csharp  
a => a.ToString()
```  
	</div>
</div>  

#### Usos más frecuentes  
```csharp  
// Eventos
accessButton.Clicked += (sender, args) => { DoAccess(); };

// Linq
var solteros = 	personas
				.Where(persona => persona.EsSoltero)
				.OrderBy(persona => persona.Nombre)
				.Select (persona => new Soltero { Nombre = persona.Nombre });
```  
De los usos más frecuentes de las expresiones lambda, podemos encontrar la asignación de manejadores de evento y en conjunto con Linq. Herramientas de las que les hablaré más adelante.

**Este es el fin de la serie de posts** que comenzó con <a href="/delegados-en-c-sharp" target="_blank">delegados en C#</a> continuando con <a href="/func-y-action-en-c-sharp" target="_blank">func y action en C#</a>. Hay otro tema en el tintero, que son los árboles de expresiones, pero aún está en preparación.