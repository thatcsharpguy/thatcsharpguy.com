---
layout: post
title: Seis características de C# 6
date: 2016-01-04 22:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: La mayoría de las características nuevas de C# 6 tienen como objeto agilizar la escritura de código y mejorar la comprensión de este cuando es revisado. Pero además acercan al lenguaje a la programación funcional.
featured_image: featured.png
github: https://github.com/ThatCSharpGuy/CsSeis
lang: es
tweet_id: 684363106651344896
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Tiene tiempo que C# 6 vio la luz, y poco a poco va siendo adoptado. Es por eso que ahora más que nunca debemos conocerlo y en este post te presento 6 de las funciones que vienen incluidas con esta versión.  
  
## using static  
Seguramente has visto cosas como `Console.WriteLine`, `Math.Pow` o inclusive `String.Format`. Como seguramente también sabrás, dichos métodos son **métodos estáticos** lo cual significa que no es necesario crear una instancia para emplearlos, por ejemplo:  
    
```csharp  
var resultado1 = Math.Pow(2, 3);
Console.WriteLine(String.Format("Resultado de resultado1 : {0}", resultado1));
```  

Puede ser que en tu aplicación exista la necesidad de emplear muchos métodos estáticos y tener que escribir el nombre completo (incluyéndo el nombre de la clase) sea muy engorroso y tedioso. Pues C# 6 alivia un poco de ese tedio, al permitirnos hacer algo como esto:

```csharp  
var resultado2 = Pow(2, 3);
WriteLine(String.Format("Resultado de resultado2: {0}", resultado2));
```  

Como puedes ver, los nombres de las clases `Console` y `Math` han desaparecido... bueno, no desaparecieron, simplemente cambiaron de lugar hacia el inicio del archivo usando las palabras reservadas `using` y `static` junto con el nombre de la clase de la que queremos usar los métodos estáticos.  

```csharp  
using static System.Math;         
using static System.Console;
```  

Las declaraciones anteriores permiten que todos los métodos de `Math` y `Console` sean accesibles en nuestra clase. Hacer esto es opcional, si te fijas bien, he dejado `String.Format(...` como estaba, pero podríamos convertirlo en un simple `Format` si agregamos `using static System.Console;`.  
 
Desde luego, esto tiene sus ventajas y sus desventajas, y es que si lo usamos sin moderación, puede llegar un momento en el que ocurra un conflicto de nombres o nos cueste trabajo entender los programas al no saber exactamente de donde viene el método que estamos usando.
  
## nameof  
Muchas veces existe la necesidad de utilizar los nombres de nuestras variables, métodos y campos como "literales" dentro de nuestro código, anteriormente teníamos que usar los llamados *códigos duros* o sacrificar un poco el desempeño usando reflexión para obtenerlos. 

En el siguiente ejemplo, la cadena `"Resultado de resultado1"`, contiene *código duro*:  
   
```csharp  
WriteLine(String.Format("Resultado de resultado1 : {0}", resultado1));
```  

En C# 6 podemos usar el operador `nameof` que toma un método, tipo o variable y regresa su nombre simple en forma de una cadena: 

```csharp  
WriteLine(String.Format("Resultado de {1} : {0}", resultado1, nameof(resultado1)));
```  

Así, al momento de renombrar `resultado1` no tendremos que buscar los lugares a los que se haga referencia a dicha variable para cambiar el nombre, todo será revisado en tiempo de compilación.  
  
Las bondades de `nameof` se pueden experimentar, entre oras cosas, cuando estamos revisando la validez de los argumentos de un método, cuando estamos desarrollando controladonres en ASP.NET MVC o cuando estamos implementando la interfaz `INotifyPropertyChanged`, lugares ne los que tendríamos que *hardcodear* los nombres de los argumentos, vistas o propiedades.  
  
## String interpolation  
Anteriormente en este post usé `String.Format` para componer una cadena de texto que se imprime a consola y mientras que el método es bastante bueno para lo que lo queremos, se puede transformar en una cadena ilegible al programador dada la forma de insertar los valores dentro de ella usando los índices de los parámetros entre corchetes:

```csharp  
WriteLine(String.Format("Resultado de {1} : {0}", resultado1, nameof(resultado1)));
```  

En el ejemplo anterior, `resultado1` será insertado en todos los lugares donde aparezca `{0}` y `nameof(resultado1)` en `{1}`, a la hora de leer la sentencia nosotros tendríamos que buscar los parámetros de acuerdo a su índice para conocer que es lo que se quiere expresar con la sentencia.  
  
Para la versión 6 del lenguaje, contamos con las *cadenas interpoladas* que son otro tipo de cadenas cuya definición comienza con el símbolo `$` seguido de la cadena deseada. La gran ventaja de las cadenas interpoladas es que se comportan como el método `String.Format` pero evitándonos tener que considerar los índices de las variables y en su lugar colocar las variables directamente, también entre corchetes.  
  
Si convertimos el ejemplo de código anterior a C# 6, sería algo como esto para obtener el mismo resultado: 

```csharp  
WriteLine($"Resultado de {nameof(resultado1)} : {resultado1}");
```  
  
Con las cadenas interpoladas se mejora la comprensión del código a la hora de escribirlo y posteriormente leerlo. Con esta sintaxis de cadena también podemos usar modificadores de formato como "d", "yyyy" o "P1".

### Un respiro  
Las tres características anteriores son entendibles sin la necesidad de crear objetos, pero para las siguientes tres sí necesitaremos crear objetos puesto que tienen que ver mucho con la definición de clases, para ello usaremos la clase `Person`, cuya definición veremos junto con las características restantes.  
  
## Auto-property enhancements  
 
Ahora es posible inicializar una <a rel="nofollow" target="_blank" href="https://msdn.microsoft.com/es-MX/library/bb384054.aspx?f=255&MSPPError=-2147217396">propiedad autoimplementada</a> desde su definición, esta tarea se podría realizar anteriormente en el constructor de la clase. Por ejemplo, cada objeto de la clase persona debe tener una edad (`Age`), en este caso se usa la inicialización para ponerle un valor por default de 0: 

```csharp  
public int Age { get; set; } = 0;
```  

También es posible realizar la asignación de un valor a las propiedades autoimplementadas que únicamente definen un `get` para acceder a ellas, estas son conocidas como de solo lectura o *"getter-only auto-properties"*, para nuestro ejemplo asumiremos que el nombre de una persona no puede ser cambiado y es por eso que se define como de solo lectura. Decidimos también que el valor por default sea "Cosme Fulanito":  

```csharp  
public string Name { get; } = "Cosme Fulanito";
```  

## Expression-bodied function members  
¿Conoces las expresiones lambda? si no, te recomiendo mi [post sobre ellas](/post/lambdas-en-c-sharp/).  

Como ya sabemos, mediante el uso de expresiones lambda es posible definir métodos, ya ahora en C# 6 esta psoibilidad se ha extendido a los métodos y propiedades definidos dentro de una clase. Las instancias de nuestra clase `Person` pueden saludar a alguien a través del método `SayHi` definido como una expresión, definición que vemos a continuación:  

```csharp  
public string SayHi() => $"Hi, my name is {Name} and I'm {Age}";

public string SayHi(string greetingPerson) => $"Hi {greetingPerson}, my name is {Name} and I'm {Age}";
```  

Para posteriormente ejecutarlo mediante una llamada como esta:
  
```csharp  
WriteLine(defaultPerson.SayHi());

WriteLine(juanito.SayHi(defaultPerson.Name));
```  

No may mucho que decir de esta característica, puesto que si se conoce el funcionamiento de las lambdas, usarlas de esta manera no es nada del otro mundo.  
  
## Null-conditional operator  
Es probable que te hayas encontrado con cascadas y cascadas de `if`s solo para comprobar si alguna propiedad de un objeto es nula o tiene valor.   

El operador *nulo-condicional* nos libera de esa tarea y funciona de esta manera: se coloca el operador `?` frente a la propiead que queremos comprobar si es nula o no, seguido de `.` y la propiedad a la que queremos acceder. Si la propiedad frente a la que está colocado `?` es nula, el resultado de la expresión será nula.
  
```csharp  
string defaultPersonParentName = defaultPerson.Parent?.Name;

WriteLine($"Default person parent name: {defaultPersonParentName}");
```  

En este caso, al no asignarle un valor a `defaultPerson.Parent`, el valor de `defaultPersonParentName` es igual a `null`, en lugar de lanzar alguna excepción. En el caso de cadenas y cualquier otro [tipo por referencia](/post/tipos-dato-c-sharp/) no tenemos que hacer nada más, puesto que estos pueden tener un valor igual a `null`.  
Sin embargo, para tipos por valor, como los enteros, es necesario convertirlos en [tipos nullables](/post/tipos-nullables-en-c-sharp) para poder hacer uso del operador, por ejemplo:    

```csharp  
int? juanitosParentAge = juanito.Parent?.Age;

WriteLine($"Juanito's parent age: {juanitosParentAge}");
```  
  
En este caso, si `juanito` no tuviera la propiedad `Parent` asignada, el entero nullable `juanitosParentAge` no tendría valor (o sería igual a nulo). También es importante notar que el operador se puede usar tantas veces como queramos, pudiendo escribir:

```csharp  
var juanitosGrandGrandGrandParent = juanito.Parent?.Parent?.Parent?.Parent;
```  

## Para finalizar  
Como se puede ver, la mayoría de las características nuevas de C# 6 tienen como objeto agilizar la escritura de código y mejorar la comprensión de este cuando es revisado. Pero además acercan al lenguaje a la programación funcional, de la cual les platicaré después.  
  
Pero tu, ¿ya usas las nuevas características? ¿crees que te resultaran útiles? cuéntame tu experiencia con ellas en <a href="https://twitter.com/intent/tweet?text=@{{ MY_TWITTER_HANDLE }} sobre {{ title }} ">Twitter</a> o envíame un correo a <a href="mailto:{{ MY_EMAIL }}?subject={{ title }}">{{ MY_EMAIL }}</a>. Recuerda que el código está en <a href="http://ideone.com/fork/FkM8i0" rel="nofollow" target="_blank"><i class="sp sp-ideone"></i> ideone</a> y en <a href="https://github.com/ThatCSharpGuy/CsSeis" target="_blank"><i class="sp sp-github"></i> GitHub</a> para que te diviertas con él. 