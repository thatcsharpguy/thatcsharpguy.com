layout: post
title: Trabajando con DocX en C#
date: 2016-02-15 19:00:00
author: Antonio Feregrino
excerpt: ¿Quieres trabajar con documentos de Word sin tener que depender de Office? DocX es la librería para ti. Con ella tienes un montón de opciones disponibles para desarrollar. Conócela aquí.
featured_image: featured.png
github: https://github.com/ThatCSharpGuy/DocX-sample
lang: es
tweet_id: 699406557037985792
tags: NuGetRecomendado
featured_tag: NuGetRecomendado

Me atrevería a decir que la gran mayoría de aplicaciones tienen una forma de reportar sus acciones al usuario a través de determinada interfaz. La interfaz más famosa es el monitor de la computadora ya sea mediante imágenes, vídeo o texto, pero no a todos lados es práctico andar cargando con una pantalla, ¿cierto? Es por eso que muchas aplicaciones también ofrecen la posibilidad de descargar su información en forma de documentos de texto, de tal forma que el usuario las pueda descargar e imprimir.  

Muchos conocen a esta acción como "Generación de informes o reportes" y hay una enorme cantidad de herramientas para realizarla, entre ellas están el `ReportViewer` de Microsoft o el Telerik, Crystal Reports de SAP, o alguna de las herramientas de Aspose. Mientras que todas esas herramientas son bastante útiles, algunas son costosas y requieren de mucha infraestructura para poder ser implementadas. Es por eso que en este post te presento una pequeña librería útil como no te imaginas que permite crear y manipular documentos de Word (únicamente los `.docx`) **sin tener instalado Office**.  
  
En esta ocasión mostraré cómo crear un reporte de las clases (`lectures`) que imparte un determinado maestro (`teacher`), con la información que usé en el <a href="http://thatcsharpguy.com/post/linq-en-c-sharp-3/">post sobre LINQ en C#</a>:  

## Creando un documento
Para crear un documento es necesario utilizar el método estático `Create` especificando la ruta del archivo. La sentencia `using` es importante para asegurarnos que cuando terminemos de usar el documento, se liberen sus recursos de manera adecuada:  

```csharp  
using (var document = DocX.Create("Prueba.docx"))
{
    // Dentro de este bloque tenemos disponible el documento
    // ...
```  

## Guardando un documento  
Una vez creado el documento y después de que hemos terminado de trabajar con él debemos guardar nuestros cambios, esto se hace mediante una llamada a su método `Save`, con lo cual todos los cambios hechos en el documento serán aplicados sobre el archivo. Es importante que llames al método dentro del bloque `using` (de otro modo obtendrás un error de compilación).  

```csharp  
    // ...
    document.Save();
}
```  

Cabe señalar que también tenemos a nuestra disposición el método `SaveAs` que permite especificar una ruta distinta a la que usamos para crear nuestro documento.  
  
Si ejecutamos el código hasta este momento veremos un documento vacío llamado "Prueba.docx" en la carpeta donde está nuestra aplicación, en mi caso es dentro de `bin\Debug`, y es porque al crear nuestro documento únicamente empleamos el nombre de un archivo.  
  
<img src="/images/docx-c-sharp__created-document.png" title=""Documento vacío"" />

## Añadiendo texto  
El texto dentro de un documento de DocX está organizado en párrafos, y por eso debemos añadir párrafos a nuestro documento, para ello debemos tomar nuestro documento y llamar el método `InsertParagraph`.  

```csharp  
var reportParagraph = document.InsertParagraph();
```  

El método devuelve una referencia al párrafo que añadió al documento, a partir de aquí podemos añadirle texto a dicho párrafo, y por ende, al documento. Para agregar texto debemos usar el método `Append` de la clase `Paragraph`, este método funciona de manera similar a un `StringBuilder` ya que podemos *"encadenar"* las llamadas para separar partes del código:  

```csharp  
reportParagraph.Append("Este es un reporte perteneciente a las clases que imparte " + teacher.GivenName + " " +
                    teacher.LastName + ", generado en " + DateTime.Now.ToShortDateString() + ". ")
    .Append("El profesor/ra " + teacher.LastName + " imparte actualmente " + lectures.Length + " asignaturas.");
```  
  
Hasta este momento, el resultado de ejecutar el código es un documento que contiene el texto arriba indicado, se ve más o menos así:  
  
<img src="/images/docx-c-sharp__sin-estilo.png" title=""Documento sin estilo"" />

### Formateando el texto  
El texto sin formato está bien, ¿pero sabes qué es mejor?  
  
DocX tiene una API peculiar para aplicar el formato, ya que todo es a través de métodos que se llaman sobre la instancia de `Pharagraph`. El formato deseado se aplica al texto que acaba de ser "escrito" con `Append`, por ejemplo:  

```csharp  
reportParagraph.Append ("Este es un reporte perteneciente a las clases que imparte ");
reportParagraph.Append (teacher.GivenName + " " + teacher.LastName).Bold().Append(", generado en ");
reportParagraph.Append (DateTime.Now.ToShortDateString ()).Italic().Append (". ")
    .Append ("El profesor/ra ").Append(teacher.LastName).Font(new FontFamily("Arial Black"))
    .Append(" imparte actualmente ")
    .Append(lectures.Length + " asignaturas.").Color(Color.Blue).Italic().Bold();
```  

En el código de arriba están pasando varias cosas:  
  
 - En la segunda línea se está poniendo el texto `teacher.GivenName + " " + teacher.LastName` en **negritas** con el método `Bold`  
 - En la tercera línea se pone en *cursiva* la fecha con `Italic` 
 - En la cuarta se cambia la fuente del texto con el que se escribe apellido del profesor con `Font`
 - En la sexta se cambia el color del texto, se pone en cursivas y en negritas también.  
 
Obtenemos un resultado como este:   
  
<img src="/images/docx-c-sharp__estilo.png" title=""Documento estilizado"" />

 
Además de estas opciones de formato también podemos subrayar, cruzar, cambiar el tamaño del texto.

## Títulos  
Una de las mejores funciones que tiene Word es la posibilidad de añadir títulos a los documentos, y DocX no se queda atrás. Como ya vimos, es necesario añadir un párrafo, agregarle texto e indicarle qué tipo de encabezado deseamos  

```csharp  
var titleParagraph = document.InsertParagraph();
titleParagraph.Append("Reporte " + LastName).Heading(HeadingType.Heading1);
```  

Y acá está el resultado:  
  
<img src="/images/docx-c-sharp__encabezado.png" title=""Encabezado"" />
  
## Encabezado y pie de página  
Para darle un poco más de formalidad al documento, vamos a añadirle un pie de página y un encabezado, otra vez debemos añadir párrafos. Antes que nada, es necesario llamar a dos métodos para preparar el terreno.

```csharp  
document.AddHeaders();
document.AddFooters();
```  

Una vez hecho esto, podemos comenzar:

```csharp  
var header = document.Headers.odd.InsertParagraph();
header.Append ("Reporte - DocX").Font(new FontFamily("Courier New"));

document.Footers.odd.PageNumbers = true;
```  

En este caso estamos agregando el texto "Reporte - DocX" al encabezado y un número de página al pie de página:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/docx-c-sharp__header.png" title=""Encabezado"" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="/images/docx-c-sharp__footer.png" title=""Pie de pagina"" />
</div>  
</div>  

## Tablas
La inserción de tablas se hace también a través de un método y especificando el número de filas y columnas que se requieren, para este caso requeriremos únicamente 3 columnas y colocaremos una fila de encabezados, también le añadiremos un poco de estilos y bordes para que se vea mejor el "reporte":

```csharp  
var table = document.InsertTable (1, 3);

table.AutoFit = AutoFit.Window;
var border = new Border (BorderStyle.Tcbs_single, BorderSize.one, 0, Color.Black);
table.SetBorder (TableBorderType.InsideH, border); // ... más bordes
```  

De ahí accedemos a la fila `0` y a sus celdas mediante la propiedad `Cells`, cada celda funciona como un nuevo documento, por lo que debemos trabajar con los párrafos como anteriormente:

```csharp  
var tableHeaders = table.Rows[0];
tableHeaders.Cells[0].InsertParagraph().Append("ID").Bold();
tableHeaders.Cells[1].InsertParagraph().Append("Clase").Bold();
tableHeaders.Cells[2].InsertParagraph().Append("Nivel").Bold();
```  

Después con un ciclo sobre `lectures`, agregamos todos los datos:

```csharp  
foreach (var lecture in lectures) 
{
    var tableRow = table.InsertRow();
    tableRow.Cells[0].InsertParagraph().Append (lecture.Id.ToString());
    tableRow.Cells[1].InsertParagraph().Append(lecture.Name);
    tableRow.Cells[2].InsertParagraph().Append(lecture.Level);
}
```  

Y el resultado:  

<img src="/images/docx-c-sharp__table.png" title=""Tabla"" />

Con esto llegamos al final de la generación de nuestro reporte, pero antes hay que decir que DocX también permite trabajar con imágenes, hipervínculos, propiedades del documento.  

# Buscar y reemplazar  
Personalmente pienso que la posibilidad de DocX de buscar y reemplazar texto en un documento es la mejor de todas, ya que podemos partir de un documento formateado a nuestro gusto (sin necesidad de crearlo desde 0) y obtener un resultado más que aceptable. Con esta característica construí una especie de "motor" para generar reportes en el trabajo.  
 
Por ejemplo, tenemos un documento llamado `template.docx`, lo abriremos con DocX usando el método `Load` y reemplazaremos algunos de sus valores para guardarlo como `out.docx`:

<img src="/images/docx-c-sharp__template.png" title=""Template"" />

```csharp  
template.ReplaceText("esta entrada", "este post sobre DocX");
template.ReplaceText("querido", "querido y respetable");
template.ReplaceText("Facebook", "Twitter");
template.ReplaceText("correo electrónico", "feregrino@thatcsharpguy.com");
```  
        
<img src="/images/docx-c-sharp__out.png" title=""Salida"" />

## Conclusión  
Al principio la API de DocX nos puede parecer un poco rara y con poca documentación, sin embargo, eso no le resta a lo enormemente útil que resulta esta librería, cuando lo que buscamos es trabajar y generar documentos de Word de manera programática. Lo mejor de todo es que **no requiere que la máquina en donde se ejecuta tenga instalado Office**, gran, gran plus.
 
# Instalación  
No olvides visitar <a href="http://docx.codeplex.com" target="_blank">la página de DocX en Codeplex</a> o instala el paquete de NuGet en tu proyecto:

```  
PM> Install-Package DocX
```  
