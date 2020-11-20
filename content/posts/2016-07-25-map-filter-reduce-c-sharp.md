---
layout: post
title: Map, reduce y filter en C#
date: 2016-07-25 19:00:00
author: Antonio Feregrino
excerpt: C# es un gran lenguaje, y unas de las cosas que me gusta de él, es que puedes programar funcionalmente aún si el lenguaje no es 100% funcional. Comparte algunas de sus características con lenguajes como Swift y F#.
featured_image: featured.png
images_folder: /map-reduce-filter/
github: https://github.com/ThatCSharpGuy/MapFilterReduce
tweet_id: 757722705835634689
lang: es
tags: AprendeCSharp, ProgramaciónFuncional
featured_tag: AprendeCSharp
---

 > Este es post es una reedición del post de <a href="https://twitter.com/giovanni_cortes" target="_blank">Giovanni Cortés</a> llamado <a href="https://giovannicortes.com/swift/map-filter-reduce-en-swift/" target="_blank">Map, filter y reduce en Swift</a>, te invito a visitarlo.  

 > También existe este post pero relacionado con F#, <a href="../map-filter-reduce-f-sharp">visítalo aquí</a>.

C# es un gran lenguaje, y unas de las cosas que me gusta de él, es que puedes programar funcionalmente aún si el lenguaje no es 100% funcional. Comparte algunas de sus características con lenguajes como Swift y F#.

Una de las características es que las los métodos pueden ser tratadas como tipos, pueden aceptar métodos como parámetros y retornar métodos. A menudo a los métodos que toman como parámetros otros métodos son llamados *funciones de orden superior*.

En este artículo vamos a enfocarnos en tres métodos que vienen incluídos en el framework (dentro de System.Linq) y que son muy poderosos cuando queremos manejar colecciones.

## Map (Select)
Imaginemos que tenemos un arreglo de números enteros y a todos los elementos queremos elevarlo al cuadrado.

Generalmente haríamos una método con un bucle como el siguiente:

```fsharp  
var array = new List<int> { 2, 3, 4, 5, 6, 7, 8, 9, 10 };

static List<int> SquareArray(List<int> arr)
{
    var result = new List<int>();
    foreach (var x in arr)
        result.Add(x * x);
    return result.ToList();
}
   
var squaredArray = SquareArray(array); // [4, 9, 16, 25, 36, 49, 64, 81, 100]
```  

El método `Select` resuelve el problema de transformar los elementos de un arreglo pasándoles una método. Este método itera sobre cada elemento y a cada uno de ellos les aplica el método que nosotros pasemos como parámetro, esto nos ahorra usar mucho los *foreach* y además de que es más elegante. `Select` retorna un una colección de la misma longitud que la coleccion a la cual le aplicamos la transformación.

El código anterior quedaría así:

```fsharp  
var array = new List<int> { 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var squaredArray = array.Select(x => x * x).ToArray(); // [4, 9, 16, 25, 36, 49, 64, 81, 100]
```  

En este caso pasamos la el método como una <a href="#" target="_blank" rel="nofollow">expresión lambda</a> `x => x * x`, donde `x` es cada elemento del arreglo pasado como parámetro de la expresión, y `x * x` es el cuerpo del método, de esa forma pudimos transformar los elementos del arreglo sin crear ningún bucle.

## Filter (Where)
Al igual que `Select`, `Where` es un método que itera sobre los arreglos. Como su nombre lo indica, filtra los elementos de un arreglo de acuerdo a la función que nosotros le pasemos.

`Where` retorna una colección con los elementos filtrados de la colección original, pudiendo ser de igual longitud, menor o vacía.

Supongamos que tenemos una colección que representa los nombres de imágenes con diferentes extensiones y queremos obtener solo los jpg:

```fsharp  
var images = new List<string> {
    "hello.jpg",
    "world.jpg",
    "hola.png",
    "mundo.png",
    "cats.jpg",
    "dogs.png"
};

public static List<string> GetJpgImages(List<string> images)
{
    var results = new List<string>();
    foreach (var image in images)
        if (image.EndsWith(".jpg"))
            results.Add(image);
    return results;
}

var jpgImages = GetJpgImages(images); // ["hello.jpg", "world.jpg", "cats.jpg"]
```  

Ahora usemos el método `Where`:

```fsharp  
var images = new List<string> {
    "hello.jpg",
    "world.jpg",
    "hola.png",
    "mundo.png",
    "cats.jpg",
    "dogs.png"
};

var jpgImages = images
                .Where(image => image.EndsWith(".jpg"))
                .ToList(); // ["hello.jpg", "world.jpg", "cats.jpg"]
```  

Al igual que con `Select`, no tuvimos que crear ni un *foreach*, ni tampoco tuvimos que agregar el elemento que estamos filtrando, solamente pasamos nuestra expresión y `Where` hace todo por nosotros.

### Reduce (Aggregate)
Al contrario que `Select` y `Where`, `Aggregte` nos retorna un solo valor, que es la combinación de los elementos de la colección a la cual le aplicamos la el método.

Prosigamos con el ejemplo de los números, si queremos sumar todos los elementos de una lista, haríamos algo como esto:

```fsharp  
var array = new List<int> { 2, 3, 4, 5, 6, 7, 8, 9, 10 };   

public static int Sum(List<int> arr)
{
    var acc = 0;
    foreach (var value in arr)
        acc += value;
    return acc;
}

var arraySum = Sum(array); // 54
```  

Cuando usamos una de las variaciones de `Aggregate`, no solamente tenemos que pasar una el metodo acumulador, si no también un acumulador, que es el valor inicial de nuestro contador, si es trabajo con enteros generalmente es 0, si son cadenas se pasa una cadena vacía. Depende de lo que quieras hacer.

Ahora el ejemplo con `Aggregate`:

```fsharp  
var array = new List<int> { 2, 3, 4, 5, 6, 7, 8, 9, 10 };

var arraySum = array.Aggregate((acc, x) => acc + x); // 54
```  

En este caso, el acumulador toma el valor inicial de 0, ese valor se pasa como parámetro a la expresión lambda, al cual le pusimos como nombre `acc`. `x` es cada elemento de la colección, y el cuerpo de nuestra lambda es la suma del acumulador y cada elemento del arreglo.

Como ves, este método es un poco más elaborado, pero una vez que le entiendas, puedes hacer muchas cosas interesantes.

## Combinando todo  

Ahora que ya sabemos el uso de estos métodos, vamos a crear un pequeño programa donde usaremos los tres al mismo tiempo.

```fsharp  
// Suponiendo que tenemos la siguiente estructura de ciudades
struct City
{
    public string Name ;
    public int Population;
}


// Vamos a definir varios ejemplos de ciudades, y meterlos en un arreglo
var paris = new City { Name = "Paris", Population = 2243 };
var madrid = new City { Name = "Madrid", Population = 3216 };
var amsterdam = new City { Name = "Amsterdam", Population = 811 };
var berlin = new City { Name = "Berlin", Population = 3397 };

var cities = new City[] { paris, madrid, amsterdam, berlin };

// Como tenemos las ciudades con poca población, vamos a escribir una función
// que nos ayude a escalar la población
static City Scale(City city) 
{
    return new City { Name = city.Name, Population = city.Population * 1000 };
}

// Ya teniendo todo, vamos a escribir todas las ciudades cuya población
// sea mayor a 1 millón de habitantes

var citiesFilter = cities.Where(city => city.Population > 1000)
                         .Select(Scale)
                         .Aggregate("City population", 
                            (result, c) => {
                                return result + $"\n{c.Name}: {c.Population}";});

Console.WriteLine(citiesFilter);

// City population
// Paris: 2243000
// Madrid: 3216000
// Berlin: 3397000
```  

Listo, tenemos nuestro programa funcionando y filtrando las ciudades, sin necesidad de usar bucles, además que gracias al acumulador usado como cadena, podemos imprimir nuestras ciudades en un lindo formato.

Espero te haya convencido de que empieces a usar estos métodos en tu programa, verás cómo vas a ir haciendo un código mucho más elegante y funcional, si quieres saber más sobre Linq, te invito a revisar <a href="#">esta serie de posts</a>.
