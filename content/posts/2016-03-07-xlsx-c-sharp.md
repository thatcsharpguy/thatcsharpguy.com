---
layout: post
title: Trabajando con XslX en C#
date: 2016-03-07 19:00:00
author: Antonio Feregrino
summary: ¿Quieres trabajar con documentos de Word sin tener que depender de Office? DocX es la librería para ti. Con ella tienes un montón de opciones disponibles para desarrollar. Conócela aquí.
featured_image: featured.png
images_folder: /xlsx-c-sharp/
github: https://github.com/ThatCSharpGuy/DocX-sample
lang: es
tags: NuGetRecomendado
featured_tag: NuGetRecomendado
---

Justamente hace unos cuantos posts, les hablaba de cómo <a href="http://thatcsharpguy.com/post/docx-c-sharp/">trabajar con documentos de Word en C#</a>, y esta vez hablo sobre algo similar... solo que con hojas de cálculo de Excel, seguramente es una tarea más requerida dada la naturaleza, ligada a los datos, de este tipo de archivos. Esto es posible a través de una librería que también se distribuye a través de NuGet, la librería en cuestión se llama EPPlus y en este post presento algunos casos de uso.  

En esta ocasión mostraré cómo crear un pequeño reporte de las clases y maestros que se imparten en una escuela, usando la misma información del <a href="http://thatcsharpguy.com/post/linq-en-c-sharp-3/">post sobre LINQ en C#</a>, para finalizar con un ejemplo de lectura de un libro de Excel en nuestra aplicación:  

## Creando un libro  
Para crear un libro de excel usaremos la clase `ExcelPackage` junto con un objeto de la clase `FileInfo`, la documentación no indica que debemos emplear nuestro código dentro de un bloque `using`, pero para evitar sorpresas, creamos el documento dentro de uno:

```csharp  
FileInfo prueba = new FileInfo("prueba.xlsx");
using (ExcelPackage excel = new ExcelPackage(prueba))
{
    var workbook = excel.Workbook;
    // ... 
```  
  
Podemos ver a la clase `ExcelPackage` como un contenedor de nuestro libro, al que podemos acceder a través de la propiedad `Workbook`. 

## Guardando un documento  
Al igual que con DocX, una vez creado el documento y después de que hemos terminado de trabajar con él debemos guardar nuestros cambios, esto se hace mediante una llamada a su método `Save`, con lo cual todos los cambios hechos en el documento serán aplicados sobre el archivo. 

```csharp  
    // ...
    excel.Save();
}
```  

Cabe señalar que también tenemos a nuestra disposición el método `SaveAs` que permite guardar una copia del documento con el que estamos trabajando, hay otra sobrecarga que inclusive nos eprmite establecer una contraseña para el archivo.  
  
Si ejecutamos el código hasta este momento veremos un archivo `.xlsx` llamado "prueba.xlsx" en la carpeta donde está nuestra aplicación, en mi caso es dentro de `bin\Debug`, y es porque al crear nuestro documento únicamente empleamos el nombre de un archivo, pero bien pudimos usar una ruta absoluta para guardarlo en otro lugar.  
  
<img src="/images/xlsx-c-sharp__new-xlsx.png" title=""Documento vacío"" />

## Agregando información  
Queremos generar un reporte con todos los maestros en una hoja de Excel y así es una de las maneras en que se puede hacer usando EPPlus
  
### Hojas de trabajo  
Para insertar datos en nuestro libro de Excel necesitamos primero agregar una hoja de trabajo o *worksheet*, lo cual se hace mediante el método `Add`, que recibe el nombre de la hoja que vamos a crear. Ten cuidado con intentar agregar dos hojas con el mismo nombre, puesto que esto no está permitido.

El método `Add` regresa una referencia a la hoja recién añadida y es precisamente usando esa referencia como vamos a añadir la información.  

```csharp  
var teacherWorksheet = wb.Worksheets.Add("Maestros");
```  

### Celdas
Partiendo de la referencia a `teacherWorksheet` podemos ahora si trabajar con las celdas (`Cells`) de esa hoja, la forma de hacerlo es como lo haríamos manualmente en el documento, es decir, mediante el sistema de coordenadas compuesto por letras y números. Por ejemplo, si queremos añadir los nombres de las columnas para nuestros datos:

```csharp  
teacherWorksheet.Cells["A1"].Value = "ID";
teacherWorksheet.Cells["B1"].Value = "Nombre";
teacherWorksheet.Cells["C1"].Value = "Apellidos";
teacherWorksheet.Cells["D1"].Value = "Email";
teacherWorksheet.Cells["E1"].Value = "Edad";
```  

Usamos la propiedad `Value` de nuestras celdas es como podemos asignarles un valor, la propiedad es de tipo `object` por lo que podemos poner ahí lo que sea.  
  
Pero, como queremos distinguir los encabezados del resto de los datos, ¿por qué no los ponemos en **negritas**?

```csharp  
teacherWorksheet.Cells["A1:E1"].Style.Font.Bold = true; 
```  

Como puedes ver, `Cells` también nos ayuda a recuperar todo un rango de celdas, para así poder trabajar con todas ellas de una sola vez.

Ya por último en un ciclo `foreach` insertamos cada uno de los valores en su correspondiente celda

```csharp  
int cell = 2;
foreach (var teacher in Database.Teachers)
{
    teacherWorksheet.Cells["A" + cell].Value = teacher.Id;
    teacherWorksheet.Cells["B" + cell].Value = teacher.GivenName;
    // ... más asignaciones
    cell++;
} 
```  

Tras lo cual obtendremos algo como esto:  
  
<img src="/images/xlsx-c-sharp__sample-1.png" title=""Documento vacío"" />

Podemos agregar más hojas a nuestro libro, también añadiremos una hoja para guardar las Clases impartidas por los maestros.

## Hoja de resumen  
Ahora veremos cómo agregar una página de resumen, en la cual mostraremos algunos datos basados en la información contenida dentro del libro.

### Combinando y centrando celdas  
Primero añadimos una nueva hoja al libro, después vamos a unir las celdas `A1` y `B1`, ponerlas en negritas y centrar el texto para el "título" de la hoja de resumen. Todo eso es posible con el siguiente código:

```csharp  
var summaryWorksheet = wb.Worksheets.Add("Resumen");

var titleCell = summaryWorksheet.Cells["A1:B1"]; // Tomamos un rango de celdas
titleCell.Merge = true;
titleCell.Style.Font.Bold = true;
titleCell.Style.HorizontalAlignment = ExcelHorizontalAlignment.Center;
titleCell.Value = "Resumen";
```  

Que nos dejará con el siguiente resultado:

<img src="/images/xlsx-c-sharp__sample-2.png" title=""Documento vacío"" />
  
### Formulas  
Para añadir fórmulas debemos usar la propiedad `Formula` de las celdas, lo que si es importante es hacer referencia a ellas a través de su nombre original en inglés.

```csharp  
summaryWorksheet.Cells["B2"].Formula = "AVERAGE(Maestros!E2:E31)";  
// ...
summaryWorksheet.Cells["B3"].Formula = "COUNTIF(Maestros!D2:D31,\"\")";
```  

En este caso estamos usando AVERAGE para sacar el promedio de edad y COUNTIF para contar la cantidad de maestros sin correo electrónico.  

<img src="/images/xlsx-c-sharp__sample-3.png" title=""Documento vacío"" />

## Usando LINQ
Una cosa que relamente es genial de EPPlus es que nos permite usar LINQ para trabajar con las hojas y celdas de nuestros libros, por ejemplo, para obtener una referencia a una hoja de trabajo existente podemos buscarla por su nombre usando el método `Single`:

```csharp  
var teacherWorksheet = wb.Worksheets.Single(ws => ws.Name == "Maestros"); 
```  

Y ahora, con la referencia `teacherWorksheet` consultamos sus celdas, nuevamente usando LINQ:

```csharp  
var cellsWithYoungTeachers = from cell in teacherWorksheet.Cells["E:E"].Skip(1)
                             where ((int)cell.Value) < 20
                             select cell;
```  

En el código anterior estamos:  
   
 - Seleccionando todas las celdas de la columna E con `.Cells["E:E"]` 
 - Brincándonos la primera de esas celdas (porque es el título) con `.Skip(1)` 
 - Filtrándo las celdas que contienen un valor menor a 20 `where ((int)cell.Value) < 20`
 
## Modificando el formato de las celdas
Después podemos iterar sobre la colección recién creada y darle un estilo diferente a las celdas que coincidieron con las condiciones que especificamos:

```csharp  
foreach (var cell in cellsWithYoungTeachers)
{
    cell.Style.Fill.PatternType = ExcelFillStyle.Solid;
    cell.Style.Fill.BackgroundColor.SetColor(Color.BlueViolet);
    cell.Style.Font.Color.SetColor(Color.Snow);
}
```  

Que nos dejará el resultado siguiente:

<img src="/images/xlsx-c-sharp__sample-4.png" title="Celdas coloreadas" />

Además de poder modificar el relleno de una celda también tenemos acceso a personalizar el borde, fuente y alineación. 

## Formato condicional
Adicionalmente a añadir nuestro propio formato, esta librería también permite trabajar con formato condicional para las celdas, y la manera de hacerlo es la siguiente:

Primero que nada, y como siempre, obtenemos una referencia a la hoja sobre la que vamos a trabajar y después determinamos sobre qué rango de columnas vamos a trabajar y creamos un objeto `ExcelAddress`. Para nuestro caso es sobre la columna de edad de los maestros que es la E:

```csharp  
var teacherWorksheet = wb.Worksheets.Single(ws => ws.Name == "Maestros");
var ageCellsStringAddress = "$E$2:$E$31";
var ageCellsAddress = new ExcelAddress(ageCellsStringAddress);
```  

Una vez que tenemos determinado el rango, accedemos a la propiedad `ConditionalFormatting` para agregar un nuevo formato condicional, en este caso usaremos una escala de dos colores: 

```csharp  
var formatting = teacherWorksheet.ConditionalFormatting.AddTwoColorScale(ageCellsAddress);
```  

Lo siguiente es configurar el formato condicional, usaremos fórmulas para obtener el valor mínimo y máximo de las edades de los profesores:

```csharp  
formatting.LowValue.Type = eExcelConditionalFormattingValueObjectType.Formula;
formatting.LowValue.Formula = "MIN(" + ageCellsAddress + ")";
formatting.LowValue.Color = Color.LightGreen;

formatting.HighValue.Type = eExcelConditionalFormattingValueObjectType.Formula;
formatting.HighValue.Formula = "MAX(" + ageCellsAddress + ")";
formatting.HighValue.Color = Color.Green;
```  

Tras lo cual el libro se verá así:

<img src="/images/xlsx-c-sharp__sample-5.png" title="Formato condicional" />

## Leyendo celdas  
Así como podemos crear hojas desde cero, también podemos leer las ya existentes. Esto es particularmente útil cuando vas a solicitar información a los usuarios a través de un libro de Excel. 

Así que supongamos que cargaremos maestros a la base con un archivo llamado `teachers.xlsx`, bastaría con abrirlo:

```csharp  
FileInfo uploaded = new FileInfo("teachers.xlsx");
using (ExcelPackage excel = new ExcelPackage(uploaded))
{
    // ...
```  

Buscar la la hoja que necesitamos y contar las filas que contiene:

```csharp  
    var teacherWorksheet = excel.Workbook.Worksheets.Single(ws => ws.Name == "Maestros");
    var cells = teacherWorksheet.Cells;
    int rowCount = cells["A:A"].Count();
```  

Y leer los datos accediendo a las celdas y su propiedad `Value`:

```csharp  
    for (int i = 1; i <= rowCount; i++)
    {
        Console.WriteLine(
            cells["A" + i].Value.ToString() + "\t" +
            cells["B" + i].Value.ToString() + "\t" +
            cells["C" + i].Value.ToString() + "\t" +
            cells["D" + i].Value.ToString() + "\t" 
            );
    }
```  

En este caso los datos leídos se imprimen a consola, pero sería sencillo insertarlos en una base de datos o hacer algun tipo de procesamiento con ellos.

## Conclusión  
Sin lugar a dudas, la API de EPPlus es bastante buena y fácil de usar y si bien tiene poca documentación, es posible encontrar recursos en línea que explícan un poco más acerca de su uso. Como ya vimos, nos puede resultar útil para generar documentos desde cero o interactuar con documentos ya existentes. Es importante remarcar que para su funcionamiento **no requiere que el lugar en donde se ejecute cuente con Office instalado**, al igual que DocX.
 
# Instalación  
No olvides visitar <a href="http://epplus.codeplex.com" target="_blank">la página de EPPlus en Codeplex</a> o instala el paquete de NuGet en tu proyecto:

```  
PM> Install-Package EPPlus
```  
