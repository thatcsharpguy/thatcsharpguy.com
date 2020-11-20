---
layout: post
title: Excepciones en C#
date: 2017-06-13 19:00:01
author: Antonio Feregrino
summary: Las excepciones son un mecanismo que podemos usar en C# para reportar errores en nuestros programas y poder manejarlos apropiadamente.
featured_image: featured.png
images_folder: /aprende-c-sharp/ex/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/excepciones
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

El framework de .NET cuenta con el mecanismo de excepciones para reportar errores que ocurren en tiempo de ejecución dentro de los programas que lo usan. Todo este mecanismo se centra alrededor de los tipos de dato `Exception`.

El tipo de dato `Exception` hereda directamente de `System.Object`, y es uno de los tipos fundamentales del framework (sin contar los nativos). Las `Exception` tienen la particularidad de poder ser usadas junto con la instrucción `throw`, que en inglés significa "lanzar", así que es muy probable que hayas previamente escuchado la expresión *"lanzar una excepción"*.

Una API puede elegir lanzar una excepción cada vez que la ejecución de un programa llega a un estado en el cual es imposible seguir ejecutándose. Algunos ejemplos en los que sucede esto son: 

 - Cuando se intenta convertir una cadena en un número, pero esta no tiene el formato adecuado se lanza una excepción del tipo `FormatException`  
 - Cuando dentro de un arreglo intentamos acceder a una posición fuera del rango de este obtendremos una del tipo: `IndexOutOfRangeException`
 - Cuando intentamos abrir un archivo no existente será `FileNotFoundException` la que aparezca
 - Cuando intentamos dividir un número entre 0 se nos aparecerá una `DivideByZeroException`

En este diagrama aparecen las algunas excepciones que existen en el framework por defecto, relacionadas por su jerarquía. Es importante señalar la jerarquía ya que tiene una utilidad muy peculiar con las excepciones que veremos más adelante:

<img src="/images/aprende-c-sharp__ex__diagram.png" title=""Exceptions"" />

## Usando `throw` para lanzar excepciones

La forma de lanzar una excepción es usando la palabra `throw` seguido de la instancia de excepción que vamos a lanzar: 

```csharp  
throw new Exception("Hey, soy el mensaje de la excepción");

var ex = new FormatException("Hey, soy una excepción guardada en una variable");
throw ex;
```  

## Excepciones personalizadas 

Mientras que las excepciones por defecto en C# son genéricas y sirven bien al framework, es probable que para nuestra aplicación no sean lo más expresivas posibles y tratar de adecuarlas puede complicarnos la escritura de código. Es por eso que, en caso de ser necesario, podemos crear nuestras propias excepciones, basta con que estas deriven de `Exception`. Por ejemplo:

```csharp  
public class TienesMuchosGatosException : Exception
// ...
```  

Ahora, ya podríamos hacer uso de esta excepción:

```csharp  
// ...
        if (value > 1)
            throw new TienesMuchosGatosException();
        _numeroGatos = value;
// ...
```  

Pero no solo eso, sino que podemos añadirle constructores y propiedades nuevas a la excepción para que esta le dé más información a quién está usando nuestro código:

```csharp  
public class TienesMuchosGatosException : Exception
{
    public int NumeroGatos { get; private set; }

    public TienesMuchosGatosException(int numeroGatos) 
        : base("¡" + numeroGatos + " son muchos gatos!")
    {
        NumeroGatos = numeroGatos;
    }
}

// En alguna otra parte del código:
// ...
        if (value > 1)
            throw new TienesMuchosGatosException(value);
        _numeroGatos = value;
// ...
```  

Como te puedes dar cuenta, estamos llamando al constructor de la clase base pasándole un mensaje (hay otro constructor que también recibe una instancia de `Exception` que podemos emplear para adjuntar cualquier otra excepción que hayamos atrapado para mayor información).

Entonces, ahora ya con estas modificaciones, si llamamos al siguiente código:

```csharp  
var adulto = new Adulto();
adulto.NumeroGatos = 10;
```  

Nuestro programa fallará, pero le dejará al desarrollador más información:

<pre>
Unhandled Exception:
Excepciones.TienesMuchosGatosException: ¡10 son muchos gatos!
  at Excepciones.Adulto.set_NumeroGatos (System.Int32 value) [0x00009] in /Users/fferegrino/Documents/github/aprende-c-sharp/excepciones/Adulto.cs:17 
  at Excepciones.Program.Main (System.String[] args) [0x00007] in /Users/fferegrino/Documents/github/aprende-c-sharp/excepciones/Program.cs:22 
[ERROR] FATAL UNHANDLED EXCEPTION: Excepciones.TienesMuchosGatosException: ¡10 son muchos gatos!
  at Excepciones.Adulto.set_NumeroGatos (System.Int32 value) [0x00009] in /Users/fferegrino/Documents/github/aprende-c-sharp/excepciones/Adulto.cs:17 
  at Excepciones.Program.Main (System.String[] args) [0x00007] in /Users/fferegrino/Documents/github/aprende-c-sharp/excepciones/Program.cs:22 
</pre>

## Manejo de excepciones

La misión de las expresiones es bastante clara: indicar que el problema no puede continuar operando en su estado actual, y como tal, terminarlo. Sin embargo, nosotros los programadores podemos *manejar* la excepción y darle un tratamiento adecuado al programa para que este siga operando. Esto se logra con el bloque `try-catch`. Este bloque cuenta con dos partes: 

- El *intentar*, en donde ponemos el código que podría generar una excepción
- El *atrapar*, en donde reaccionaremos eventualmente a la excepción ocurrida

Su sintaxis es así:  

```csharp  
var adulto2 = new Adulto();
try
{
    // Intentamos algo peligroso:
    adulto2.NumeroGatos = Int32.MaxValue;
}
catch (TienesMuchosGatosException te)
{
    // Manejamos la excepción relacionada con los gatos:
    Console.WriteLine("Ooops, no puedes tener " + te.NumeroGatos + " gatos");
}
```  

### Manejo selectivo de excepciones 

En el código anterior únicamente estamos manejando excepciones del tipo `TienesMuchosGatosException`, pero ¿qué pasaría si al establecer la propiedad también pudiéramos obtener excepciones de otro tipo? pues para eso podemos combinar los bloques `catch`: 

```csharp  
try
{
    // Intentamos algo peligroso:
    adulto2.NumeroGatos = Int32.MaxValue;
}
catch (TienesMuchosGatosException te)
{
    // Manejamos la excepción relacionada con los gatos:
    Console.WriteLine("Ooops, no puedes tener " + te.NumeroGatos + " gatos");
}
catch(NullReferenceException ne)
{
    // Manejamos el error relacionado con objetos nulos
} 
catch(ArithmeticException)
{
    // Manejamos cualquier error relacionad 
    // con desborde de números o divisiones entre 0
}
catch
{
    // Manejamos cualquier otra excepcion
}
```  

A estos bloques de captura de excepciones se decide si se accede o no de manera secuencial, es decir, en caso de que ocurra una excepción primero verifica si la excepción es del tipo `TienesMuchos...`, si no, verifica si es del tipo `NullReference...`, si no, verifica si es del tipo `Arithmetic...` y si no es cualquiera, entra el último bloque que atrapará cualquier excepción.

### Variantes de `catch`  
En el ejemplo de código anterior hay dos variantes de `catch`. La primera, que no especifica un identificador para la exepción (`catch(ArithmeticException)`) que usamos para cuando sólo nos interesa más que saber qué tipo de excepción ocurrió y no conocer más detalles de esta. Y la segunda `catch` que es una forma corta de escribir `catch(Exception)`, y esta se usa para cuando lo que nos interesa es saber que ocurrió una excepción pero no nos interesa ni de qué tipo fue.

### La jerarquía en el `catch`  

Es en el `catch` en donde es importante la jerarquía de las excepciones. Por ejemplo, en el código anterior, el bloque `catch(ArithmeticException)` atrapará tanto las de tipo `NotFiniteNumberException`, `OverflowException`, y `DivideByZeroException`. Porque estas heredan de `ArithmeticException`. 

En otras ocasiones verás (o utilizarás) la vieja confiable `catch(Exception e)`, que básicamente atrapará todo lo que pase por ella, puesto que `Exception` es la clase base. Hay contados casos en los que diría que es recomendable emplear este `catch` ya que, al capturar **cualquier** tipo de excepción, puede ser que tu programa esté fallando en alguna parte que tu no tenías contemplada y no darte cuenta porque "nunca" ocurrió una excepción.

### Relanzando excepciones  

Hay ocasiones en las que tal vez quieras manejar una excepcion capturada con el único fin de realizar alguna tarea de registro, pero dejar que siga fallando como si no la hubieras atrapado. Para esos casos, puedes usar la instrucción `throw` solamente:

```csharp  
try
{
    throw new NotImplementedException();
}
catch(Exception e)
{
    // Logging
    throw;
}
```  

Tal vez te sientas tentado a colocar el identificador para relanzar la excepción (`throw e;`), pero esto lo que hará es destruir información potencialmente valiosa que ya contiene la excepción, así que no deberías hacerlo.  
