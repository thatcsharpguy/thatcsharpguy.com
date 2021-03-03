---
layout: post
title: Map, reduce y filter en F#
slug: map-filter-reduce-c-sharp
date: 2016-07-25 19:00:00
author: Antonio Feregrino
summary: F# es un gran lenguaje, y unas de las cosas que me gusta de él es que está orientado completamente a la programación funcional. Comparte varias características con lenguajes como Elixir y otras pocas con lenguajes como Swift.
featured_image: featured.png
images_folder: /map-reduce-filter/
github: https://github.com/ThatCSharpGuy/MapFilterReduce
tweet_id: 757723414643650560
lang: es
tags: AprendeFSharp, ProgramaciónFuncional
featured_tag: AprendeFSharp
---

 > Este es post es una reedición del post de <a href="https://twitter.com/giovanni_cortes" target="_blank">Giovanni Cortés</a> llamado <a href="https://giovannicortes.com/swift/map-filter-reduce-en-swift/" target="_blank">Map, filter y reduce en Swift</a>, te invito a visitarlo.  

 > También existe este post pero relacionado con C#, <a href="../map-filter-reduce-c-sharp">visítalo aquí</a>.  
  
F# es un gran lenguaje, y unas de las cosas que me gusta de él es que está orientado completamente a la programación funcional. Comparte varias características con lenguajes como Elixir y otras pocas con lenguajes como Swift.

Una de las características es que las funciones son tratadas como tipos, pueden aceptar funciones como parámetros y retornar funciones. A menudo a las funciones que toman como parámetros otras funciones son llamadas *funciones de orden superior*.

En este artículo vamos a enfocarnos en tres funciones que vienen incluídas en F# y que son muy poderosas cuando queremos manejar colecciones.

## Map
Imaginemos que tenemos un arreglo de números enteros y a todos los elementos queremos elevarlo al cuadrado.

Generalmente haríamos una función con un bucle como el siguiente:

```fsharp  
let array = [| 2; 3; 4; 5; 6; 7; 8; 9; 10 |]

let squareArray arr = 
    let result = new List<int>()
    for x in arr do
        result.Add(x * x)
    result.ToArray()
   
let squaredArray = squareArray array  // [4, 9, 16, 25, 36, 49, 64, 81, 100]
```  

La función `map` resuelve el problema de transformar los elementos de un arreglo pasándoles una función. Esta función itera sobre cada elemento y a cada uno de ellos les aplica la función que nosotros pasemos como parámetro, esto nos ahorra usar mucho los *for...in...do* y además de que es más elegante. `map` retorna un arreglo de la misma longitud que el arreglo al cual le aplicamos la transformación.

El código anterior quedaría así:

```fsharp  
let array = [| 2; 3; 4; 5; 6; 7; 8; 9; 10 |]

let squaredArray = 
    array
    |> Array.map(fun x -> x * x)  // [4, 9, 16, 25, 36, 49, 64, 81, 100]
```  

En este caso pasamos la función como una <a href="#" target="_blank" rel="nofollow">expresión lambda</a> `fun x -> x * x`, donde `x` es cada elemento del arreglo pasado como parámetro de la expresión, y `x * x` es el cuerpo de la función, de esa forma pudimos transformar los elementos del arreglo sin crear ningún bucle.

## Filter
Al igual que `map`, `filter` es una función que itera sobre los arreglos. Como su nombre lo indica, filtra los elementos de un arreglo de acuerdo a la función que nosotros le pasemos.

`filter` retorna un arreglo con los elementos filtrados del arreglo original, pudiendo ser de igual longitud, menor o vacío.

Supongamos que tenemos un arreglo que representa los nombres de imágenes con diferentes extensiones y queremos obtener solo los jpg:

```fsharp  
let images = [| 
    "hello.jpg"
    "world.jpg" 
    "hola.png" 
    "mundo.png"
    "cats.jpg" 
    "dogs.png" |]

let getJpgImages images : string array = 
    let result = new List<string>()
    for x : string in images do
        if (x.EndsWith(".jpg")) then result.Add(x)
    result.ToArray()

let jpgImages = getJpgImages images // ["hello.jpg", "world.jpg", "cats.jpg"]
```  

Ahora usemos la función `filter`:

```fsharp  
let images = [| 
    "hello.jpg"
    "world.jpg" 
    "hola.png" 
    "mundo.png"
    "cats.jpg" 
    "dogs.png" |]

let jpgImages = 
    images
    |> Array.filter(fun image -> image.EndsWith(".jpg")) // ["hello.jpg", "world.jpg", "cats.jpg"]
```  

Al igual que `map`, no tuvimos que crear ni un *for...in...do*, ni tampoco tuvimos que agregar el elemento que estamos filtrando, solamente pasamos nuestra expresión y `filter` hace todo por nosotros.

**NOT** Cabe destacar que F# infiere el tipo de la variable gracias al arreglo original, por eso podemos usar el método `EndsWith` a cada elemento porque se sabe que es un tipo cadena.

### Reduce
Al contrario que `map` y `filter`, `reduce` nos retorna un solo valor, que es la combinación de los elementos del arreglo al cual le aplicamos la función.

Prosigamos con el ejemplo de los números, si queremos sumar todos los elementos de un arreglo, haríamos algo como esto:

```fsharp  
let array = [| 2; 3; 4; 5; 6; 7; 8; 9; 10 |]

let sum arr : int = 
    let mutable result : int = 0
    for x in arr do
        result <- result + x
    result

let arraySum = sum(array); // 54
```  

Cuando usamos `reduce`, no solamente tenemos que pasar una función, si no también un acumulador, que es el valor inicial de nuestro contador, si es trabajo con enteros generalmente es 0, si son cadenas se pasa una cadena vacía. Depende de lo que quieras hacer.

Ahora el ejemplo con `reduce`:

```fsharp  
let array = [| 2; 3; 4; 5; 6; 7; 8; 9; 10 |]

let arraySum = 
    array
    |> Array.reduce(fun acc x -> acc + x) // 54
```  

En este caso,  el acumulador toma el valor inicial de 0, ese valor se pasa como parámetro a la expresión lambda, al cual le pusimos como nombre `acc`. `x` es cada elemento del arreglo, y el cuerpo de nuestra lambda es la suma del acumulador y cada elemento del arreglo.

Como ves, esta función es un poco más elaborada, pero una vez que le entiendas, puedes hacer muchas cosas interesantes.

## Combinando todo  

Ahora que ya sabemos el uso de estas funciones, vamos a crear un pequeño programa donde usaremos las tres al mismo tiempo.

```fsharp  
// Suponiendo que tenemos la siguiente estructura de ciudades
type City = 
    struct
        val Name : string
        val Population : int
        new(name : string, population : int) = 
            { Population = population
              Name = name }
    end


// Vamos a definir varios ejemplos de ciudades, y meterlos en un arreglo
let paris = new City("Paris", 2243)
let madrid = new City("Madrid", 3216)
let amsterdam = new City("Amsterdam", 811)
let berlin = new City("Berlin", 3397)

let cities = [|paris;madrid;amsterdam;berlin|]

// Como tenemos las ciudades con poca población, vamos a escribir una función
// que nos ayude a escalar la población
let scale(city : City ) : City =
    new City ( city.Name, city.Population * 1000)

// Ya teniendo todo, vamos a escribir todas las ciudades cuya población
// sea mayor a 1 millón de habitantes
let citiesFilter = 
    cities
    |> Array.filter(fun city -> city.Population > 1000)
    |> Array.map(scale)
    |> Array.fold(fun result c -> result + sprintf "\n%s: %d" c.Name c.Population ) "City population"

System.Console.WriteLine(citiesFilter)

// City population
// Paris: 2243000
// Madrid: 3216000
// Berlin: 3397000
```  

Listo, tenemos nuestro programa funcionando y filtrando las ciudades, sin necesidad de usar bucles, además que gracias al acumulador usado como cadena, podemos imprimir nuestras ciudades en un lindo formato.

Espero te haya convencido de que empieces a usar estas funciones en tu programa, verás cómo vas a ir haciendo un código mucho más elegante y funcional.
