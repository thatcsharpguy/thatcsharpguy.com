---
layout: post
title: AutoMapper
date: 2016-03-28 19:00:00
author: Antonio Feregrino
excerpt: AutoMapper es un mapeador objeto-objeto que &quot;convierte&quot; un objeto de un tipo a otro, sin embargo lo que hace realmente útil a esta herramienta es que facilita la tarea mediante el uso de convenciones de código.
featured_image: featured.png
github: https://github.com/ThatCSharpGuy/AutoMapper-Sample
lang: es
tweet_id: 714623099140833281
tags: NuGetRecomendado
featured_tag: NuGetRecomendado
---

Hay muchas razones por las que como desarrolladores nos podemos ver en la necesidad de transferir, o mapear, información de un objeto a otro. Una de estas razones es para evitar el <a href="https://en.wikipedia.org/wiki/Mass_assignment_vulnerability" target="_blank" rel="nofollow">Overposting</a>.

En general se emplea el mapeo en los límites entre componentes de un sistema, ya sea de la base de datos a la capa de lógica, de la lógica a la interfaz gràfica o de un servicio web de un tercero a nuestra app. El hecho de que existan los mapeos favorece la separación de responsabilidades en el código.

Por ejemplo, cuando estamos trabajando con servicios web y recibimos un objeto simple y lo queremos transformar en algo con lo que nuestra aplicaciòn pueda trabajar, otro ejemplo es cuando somos nosotros los que enviamos la información de nuestra aplicación a través de un servicio web, sin embargo no queremos que el receptor tenga acceso a toda la información que tenemos disponible. 

## Ejemplo práctico
Supongamos que tenemos un servicio web que sirve para consultar información sobre libros, de nuestra base de datos recuperamos la información a través de las siguientes entidades:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class BookEntity
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Subtitle { get; set; }
    public int Pages { get; set; }
    public int Edition { get; set; }
    public AuthorEntity Author { get; set; }
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class AuthorEntity
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public List<BookEntity> Books { get; set; }

}
  

```  
</div>  
</div>

Responder a la petición de un usuario con dichas clases, representaría dar más información de la que se necesita, es por eso usaremos estas clases para responder a cada petición:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class BookModel
{
    public string FullTitle { get; set; }
    public int Pages { get; set; }
    public int Edition { get; set; }
    public string AuthorName { get; set; }
    public DateTime TimeSent { get; set; }
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
public class AuthorModel
{
    public string Name { get; set; }
    public int Age { get; set; }
    public int BooksCount { get; set; }
}
  
  
```  
</div>  
</div>

Ahora bien, podríamos pensar en hacerlo manualmente:

```csharp  
var modeloAutor = new AuthorModel();
modeloAutor.Name = entidadAutor.Name;
modeloAutor.Age = entidadAutor.Age;
modeloAutor.BooksCount = entidadAutor.Books != null ? entidadAutor.Books.Count : 0;
```  

Pero no es una tarea sencilla si nuestros objetos comienzan a crecer en tamaño o complejidad... y ni hablar de lo aburrido que puede llegar a ser escribir cada mapeo "a mano". 

## AutoMapper  
Es por eso que a <a href="https://github.com/jbogard" target="_blank" rel="nofollow">Jimmy Bogard</a> se le ocurrió la idea de crear AutoMapper, que es una pequeña librería que nos ayudará a automatizar esta tarea. 

AutoMapper es un mapeador objeto-objeto que "convierte" un objeto de un tipo a otro, sin embargo lo que hace realmente útil a esta herramienta es que facilita la tarea mediante el uso de convenciones de código, que le ayudan a decidir qué propiedad de un tipo se mapea al otro. 

### Configuración
Para comenzar a usar AutoMapper demebos configurarlo, para lo cual utilizaremos una instancia de la clase `MapperConfiguration`, a través del que configuraremos el mapeo:

```csharp  
var automappingConfiguration = new AutoMapper.MapperConfiguration(config =>
{
    // Configurar mapeo
```  

#### Convenciones  

A partir de aquí se tiene que configurar cada mapeo objeto-objeto individualmente, necesitaremos dos tipos de dato una *fuente* y un *destino*, utilizando la variable `config` y su método `CreateMap` es como se realiza la tarea. Por ejemplo, para configurar el mapeo de `AuthorEntity` (la fuente) a `AuthorModel` (el destino) basta con una sola línea:

```csharp  
config.CreateMap<AuthorEntity, AuthorModel>();
```  

Esto se debe a que para este mapeo utiliza las convenciones definidas por AutoMapper:  
  
 - Ambos tienen `Nombre` del tipo `string`  
 - Ambos tienen `Age` del tipo `int`
 - para `BooksCount` en el destino, AutoMapper busca una colección `Books`, y obtendrá el número de elementos que contiene.
 
 Esto hace que el mapeo sea automático entre las dos propiedades, estas convenciones están incluídas en la librería, sin embargo <a href="https://lostechies.com/joshuaflanagan/2010/03/19/teaching-automapper-about-our-conventions/" target="_blank" rel="nofollow">podemos definir las nuestras</a>. 
 
#### Personalizado  
 
 Si tenemos unpar de objetos que requieren de un mapeo más complejo, tenemos a nuestra disposición algunos métodos adicionales, que nos ayudarán a configurar a mayor profundidad AutoMapper, toma por ejemplo las dos entidades `BookEntity` (la fuente) a `BookModel` (el destino):
 
```csharp  
config.CreateMap<BookEntity, BookModel>()
    .ForMember(dest => dest.AuthorName, 
        opt => opt.ResolveUsing(entity => entity.Author.Name))
    .ForMember(dest => dest.FullTitle,
        opt => opt.ResolveUsing(entity => entity.Title + " " + entity.Subtitle))
    .ForMember(dest => dest.TimeSent, opt => opt.Ignore());
```  
 
Veamos por partes lo que está sucediendo en el código anterior:

 1. Con `config.CreateMap<BookEntity, BookModel>()` estamos indicando el inicio de una configuración de mapeo.  
 2. Con el método `ForMember` le estamos indicando que la propiedad `AuthorName` en el destino la debe obtener de `Author.Name` en la fuente
 3. Al igual que en la anterior, le estamos diciendo que `FullTitle` la obtenga de concatenar `Title`, un espacio en blanco y `Subtitle` de la fuente
 4. El destino tiene una propiedad llamada `TimeSent` que no existe en la fuente, para asegurar que el mapeo sea correcto, le decimos a la configuración que la ignore mediante el método `Ignore`.

Para este ejemplo no se requiere de una configuración más compleja, sin embargo si tu aplicación la requiere, puedes checar <a href="https://github.com/AutoMapper/AutoMapper/wiki/Custom-value-resolvers" target="_blank" rel="nofollow"><i>custom resolvers</i></a>, <a href="https://github.com/AutoMapper/AutoMapper/wiki/Custom-type-converters" target="_blank" rel="nofollow"><i>custom type converters</i></a>, <a href="https://github.com/AutoMapper/AutoMapper/wiki/Conditional-mapping" target="_blank" rel="nofollow">mapeo condicional</a> o cualquier otra de las posibilidades que puedes consultar en la <a href="https://github.com/AutoMapper/AutoMapper/wiki" target="_blank" rel="nofollow">documentación de AutoMapper</a>. 

### Uso

Una vez que hemos <a href="#configuracion">configurado</a> el mapeo, debemos llamar al método `CreateMapper` de la clase `MapperConfiguration`

```csharp  
var mapper = automappingConfiguration.CreateMapper();
```  

la cual nos devolverá una instancia de un objeto que implementa `IMapper`, que es a través del cual se realizará cada mapeo. Luego, con esta instancia a nuestra disposición, bastará con llamar a `Map` pasándole una instancia de la fuente (desde la que queremos mapear) y el tipo del destino (al que queremos llegar):

```csharp  
// De AuthorEntity a AuthorModel
var modeloAutor = mapper.Map<AuthorModel>(entidadAutor);

// De BookEntity a BookModel
var modeloLibro = mapper.Map<BookModel>(bookEntity);
```  

Como podrás ver, podemos centralizar bastante la tarea de mapeo, y es que en tu aplicación **basta con tener una sola referencia a `IMapper`**, así que es lo normal que únicamente la asignes una vez y la reutilizes a través de una propiedad estática o un patrones de diseño de aplicaciones como DI o singleton.

## Conclusión
Este post apenas y roza la superficie de todo las posibilidades que AutoMapper tiene para nosotros y no cabe duda de que después de adentrarte en la documentación podrás encontrarle más utilidad. Además de que AutoMapper también es fácilmente integrable con patrones de diseño, incluyendo la Inyección de Dependencias. Si existe algo en lo que estés atorado con la configuración, tal vez pueda ayudar.
 
# Instalación  
Para instalarlo no hay mejor manera que el gestor de paquetes de NuGet:

```  
PM> Install-Package AutoMapper
```  

O échale un ojo al <a href="https://github.com/AutoMapper/AutoMapper" target="_blank" rel="nofollow">proyecto en GitHub</a> y contribuir a su desarrollo de alguna manera.