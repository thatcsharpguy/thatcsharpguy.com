---
layout: post
title: virtual, override y new
date: 2017-07-25 19:00:01
author: Antonio Feregrino
summary: Las palabras new, virtual y override nos ayudan a especificar el comportamiento de los miembros de clases y sus subclases.
featured_image: featured.png
images_folder: /aprende-c-sharp/new/
github: https://github.com/ThatCSharpGuy/aprende-c-sharp/tree/master/new
lang: es
tags: AprendeCSharp
featured_tag: AprendeCSharp
---

Comencemos hablando de perritos, o de *Canis lupus familiaris* para ser más exactos.  

Toma, por ejemplo, la clase `CanisLupusFamiliaris` (perro doméstico), que tiene dos métodos, nota que uno está marcado como `virtual` y otro no. 

```csharp  
public class CanisLupusFamiliaris // Perro doméstico
{
    public virtual void Ladrar()
    {
        Console.WriteLine("Ladrido genéico");
    }

    public void MoverOrejas()
    {
        Console.WriteLine("Error: sin información");
    }
}
```  

Y una clase derivada, llamada `Chihuahua`, que como sabrás, es un perro doméstico. El Chihuahua, al ser algo más específico tiene su propia manera de ladrar y mover las orejas, define sus propios métodos:

```csharp  
public class Chihuahua : CanisLupusFamiliaris
{
    public void Ladrar()
    {
        Console.WriteLine("Bau, bau");
    }

    public void MoverOrejas()
    {
        Console.WriteLine("Mueve sus orejas peludas");
    }
}
```  

### `new`, `override` 

Sin embargo, el `Chihuahua` no es el único perro, también tenemos un `GranDanes`, que nuevamente, es un perro doméstico y hereda de `CanisLupusFamiliaris`. Pero, a diferencia de la clase anterior, esta implementa su forma de ladrar y de mover la cola de manera distinta:

```csharp  
public class GranDanes : CanisLupusFamiliaris
{
    public override void Ladrar()
    {
        Console.WriteLine("Woof, woof");
    }

    public new void MoverOrejas()
    {
        Console.WriteLine("Mueve sus orejas gigantezcas");
    }
}
```  

Si te das cuenta, aparecieron dos palabras en la definición de los métodos: `new` y `override` 

La explicación de estas palabras es la siguiente:  

## `override`  

**El modificador `override` se usa para indicar que el método que se está definiendo modificará el comportamiento del definido en la clase base.** Para que un miembro pueda ser sobrescrito debe estar marcado con el modificador `virtual`. La clase `GranDanes` está sobrescribiendo el método virtual `Ladrar`. Entonces no importa desde donde llamemos a este método, siempre llamaremos a la implementación de esta clase.

Si compilas el código anterior aparecerán unas advertencias en tu IDE, indicándote que en la clase `Chihuahua` el método `Ladrar` esconde al método del mismo nombre en la clase `CanisLupusFamiliaris`, sin embargo, al estar marcado como virtual también nos da la opción de marcarlo con `override`:

<img src="/images/aprende-c-sharp__new__overrideWarning.jpg" title="Hiding member" />

Como la clase `GranDanes` sí está sobrescribiendo el archivo, estas advertencias no aparecen.

## `new`  

En este ámbito (antes de la definición de un método o propiedad de una clase) nos ayuda a especificar que la definición de nuestro método está *ocultando* una definición similar en la clase padre. En el caso de `GranDanes`, el método `MoverOrejas` está escondiendo la definición de la clase padre. **En realidad la palabra `new` no es más que una indicación para el compilador de que estás conciente de que estás escondiendo una definición** ya existente, no tiene otro efecto en tu programa.

Si compilas el código anterior aparecerán unas advertencias en tu IDE, indicándote que en la clase `Chihuahua` el método `MoverOrejas` esconde al método del mismo nombre en la clase `CanisLupusFamiliaris`:

<img src="/images/aprende-c-sharp__new__newWarning.jpg" title="Hiding member" />

Esta advertencia no aparece en la clase `GranDanes`.

## Ejemplo:

Considera el siguiente programa, en donde declaramos un perro de la clase base y llamamos a sus métodos, nada raro, todo funciona como debería funcionar:  

  <div class="wrapper pure-g">
      <div class="pure-u-1 pure-u-md-14-24">
```csharp  
CanisLupusFamiliaris canis = new CanisLupusFamiliaris();
Console.WriteLine("Canis genérico:");
canis.Ladrar();
canis.MoverOrejas();
```  
      </div>
      <div class="pure-u-1 pure-u-md-10-24">
<pre>
// En pantalla:
Canis genérico:
Ladrido genérico
Error: sin información
</pre>
      </div>
  </div>

En el siguiente bloque creamos un `Chihuahua` pero lo asignamos a un objeto de la clase base, después, al llamar a los métodos, vemos que en realidad está llamando a los métodos de la clase base, puesto que no está sobrescribiéndolos con `override`:  

  <div class="wrapper pure-g">
      <div class="pure-u-1 pure-u-md-14-24">
```csharp  
CanisLupusFamiliaris chihuahuaCanis = new Chihuahua();
Console.WriteLine("Chihuahua (Canis):");
chihuahuaCanis.Ladrar();
chihuahuaCanis.MoverOrejas();
```  
      </div>
      <div class="pure-u-1 pure-u-md-10-24">
<pre>
// En pantalla:
Chihuahua (Canis):
Ladrido genérico
Error: sin información
</pre>  
      </div>
  </div>

En el siguiente bloque creamos un `Chihuahua`, ahora si asignándolo a un objeto de su misma clase, la llamada a los métodos nos da el resultado esperado:  

  <div class="wrapper pure-g">
      <div class="pure-u-1 pure-u-md-14-24">
```csharp  
Chihuahua chihuahua = new Chihuahua();
Console.WriteLine("Chihuahua:");
chihuahua.Ladrar();
chihuahua.MoverOrejas();
```  
      </div>
      <div class="pure-u-1 pure-u-md-10-24">
<pre>
// En pantalla:
Chihuahua:
guau, guau
Mueve sus orejitas
</pre> 
      </div>
  </div>  

En el siguiente bloque creamos un `GranDanes`, asignándolo a un objeto de la clase base, la llamada a los métodos nos da resultados mixtos, `Ladrar` accede a la implementación de `GranDanes` porque sobrescribimos el método, pero `MoverOrejas` accede al de la clase base:  

  <div class="wrapper pure-g">
      <div class="pure-u-1 pure-u-md-14-24">
```csharp  
CanisLupusFamiliaris granDanesCanis = new GranDanes();
Console.WriteLine("Gran danes (Canis):");
granDanesCanis.Ladrar();
granDanesCanis.MoverOrejas();
```  
      </div>
      <div class="pure-u-1 pure-u-md-10-24">
<pre>
// En pantalla:
Gran danes (Canis):
WOOF! WOOF!
Error: sin información
</pre>
      </div>
  </div>  

En el siguiente bloque creamos un `GranDanes`, asignándolo a una clase de su tipo, las llamadas a los métodos se hacen directamente a este tipo: 
  
  <div class="wrapper pure-g">
      <div class="pure-u-1 pure-u-md-14-24">
```csharp  
GranDanes granDanes = new GranDanes();
Console.WriteLine("Gran danes:");
granDanes.Ladrar();
granDanes.MoverOrejas();
```  
      </div>
      <div class="pure-u-1 pure-u-md-10-24">
<pre>
// En pantalla:
Gran danes:
WOOF! WOOF!
Mueve sus orejas gigantezcas
</pre> 
      </div>
  </div>  

## Para terminar  

Como vimos, las palabras `virtual` y `override` están completamente relacionadas y nos ayudan a preservar el comportamiento definido en las subclases de un tipo cuando se hace referencia a ellos desde el tipo base. Por otro lado, `new` no es más que un indicador para que el compilador no piense que estamos cometiendo un error si nombramos un miembro de una clase como uno de los que contenga su clase base.  

Por cierto, en este ejemplo solamente utilicé métodos, pero las tres palabras de las que hablé en este post aplican para cualquier miembro: campos, propiedades, métodos, eventos, operadores, indizadores y constructores.
