---
title:  La inyección de dependencias
date:  2016-09-05 18:00:00
youtube_id: lzfPJdurQIc
images_folder:  /tv/di/
summary:  La inyección de dependencias es un patrón de diseño que nos permite implementar en nuestras aplicaciones el principio de inversión de control. Consiste en evitar la instanciación de unos objetos dentro de otros, evitando así el acoplamiento fuerte.
featured_image:  featured.jpg
github:  https://github.com/ThatCSharpGuy/patterns
tweet_id:  772914100062093316
tags:  Meta, Tv
featured_tag:  Tv
layout: video
---

Antes de hablar de la inyección de dependencias es necesario conocer dos conceptos:  

 - Los patrones de diseño  
 - El principio de inversión    

# Patrón de diseño de software  
En el mundo de desarrollo de software, cuando hablamos de un patrón de diseño, nos referimos a una forma de resolver un "problema" recurrente a la hora de diseñar una aplicación. Podemos ver un patrón como una plantilla genérica, independiente del lenguaje de programación que usemos, en la que podemos basarnos para comenzar a escribir el código de una app.  

Son los programadores quienes deciden qué es un patrón de diseño y qué no, es decir, no existe una especie de organismo que diga qué sí es y qué no... así que generalmente para que algo sea considerado un patrón debe ser una solución comprobada por su efectividad en sus usos previos. Pero siempre recuerda que

 > Los patrones de diseño son una opción, no un requerimiento

### El principio de inversión  

Este principio, inicialmente definido en el artículo <a href="http://www.laputan.org/drc/drc.html" target="_blank">Designing Reusable Classes</a> de Ralph Johnson y Brian Foote en 1988, consiste en intentar eliminar las dependencias de un módulo de un programa con el resto.  

Piensa en un programa *tradicional*, en el programa decide **qué sucede**, **cómo sucede** y **cuándo sucede**. Para que un programa así funcione, los módulos deben tener conocimiento total sobre aquellos otros submódulos que utiliza para funcionar, lo cual genera un acoplamiento fuerte.

Mientras que uno que implementa la inversión de control **no debe saber cómo suceden las cosas**, solamente qué sucede y cuándo, alguien más (generalmente un framework) le entrega instancias o referencias de los módulos con los que tiene que trabajar, obteniendo así un acoplamiento débil.  

Por poner un ejemplo, un poco absurdo pero que creo que funciona: imagínate que tú eres un programa y de pronto te da hambre de una pizza.  

Si fueras un programa *tradicional* **dependerías** que saber cómo preparar una pizza, desde obtener los ingredientes, mezclarlos del modo adecuado y esperar el tiempo necesario en el horno.  

Pero si fueras un programa que implementa el principio de inversión de control, existirían dos opciones para conseguirla:

 - Tendrías que saber en dónde conseguir la pizza (<a href="https://en.wikipedia.org/wiki/Service_locator_pattern" target="_blank">Patrón de ubicación de servicios</a>)  
 - Alguien más te la entregaría (inyección de dependencias)

De ambas formas se reduce **dependencia** a saber cómo prepararla tu mismo.  

Y no solo eso, si más adelante quieres otro tipo de comida (por ejemplo, una hamburguesa), podrías obtenerla sin problema, mientras que de la forma tradicional tendrías que aprender a prepararlas.  

## Inyección de dependencias  

Ahora sí, hablemos del tema. En la inyección de dependencias, cuyo nombre <a href="http://www.martinfowler.com/articles/injection.html#FormsOfDependencyInjection" target="_blank">se le atribuye a Martin Fowler</a>, podemos encontrar cuatro roles:  

- Las interfaces que definen las operaciones de los servicios - no necesariamente tienen que ser interfaces (como en C#), sino que puede ser una clase abstracta
- Los servicios, que implementan las interfaces anteriores, que se van inyectar 
- Los clientes que usarán los servicios
- El inyector, que es el encargado de construir e inyectar los servicios en los clientes  

Estos roles no son completamente exclusivos, es decir, un cliente puede ser un servicio que deba ser inyectado a otro cliente.  

Basándonos en los roles anteriores, el flujo de programación de una aplicación que implementa la inyección de dependencias es el siguiente:  

1. Creas las abstracciones que definen el comportamiento de los servicios. Siguiendo con el ejemplo de los alimentos, imagínate una interfaz `IAlimento`, que defina la propiedad `Nombre` o, el método `Consumir()`:  
  
```csharp  

```  


2. Implementas dicho comportamiento para cada servicio, digamos que  tenemos los alimentos `Pizza` y `Hamburguesa`, que implementan la interfaz 

```csharp  

```  


3. Creas los clientes que consumirán dichos servicios, por ejemplo, `Persona`, siempre especificando una manera de inyectar las dependencias, ya sea a través del constructor o un método.    

```csharp  

```  

4. Se configura el inyector, el inyector es el único que tiene conocimiento sobre todas las dependencias, ya sea que usemos un framework o lo hagamos "a mano" 

```csharp  

```  

En este proceso me parece que hay dos partes que requieren de un poco de trabajo:  

- A la hora de diseñar tu app: definir la interfaz de los servicios  
- A la hora de escribir el código: configurar el inyector de dependencias

Para la primera no hay más que diseñar bien la app, para la segunda nos podemos ayudar de un framework  

### Frameworks  

Para implementar la DI hay muchos frameworks que realizan la tarea del inyector. Sin embargo, a estos frameworks usualmente se les conoce como Contenedores de DI, porque implementan una especie de contenedor o almacén en donde se guardan las dependencias para cuando necesiten ser resueltas en tiempo de ejecución. 

Entre los frameworks, por ejemplo, para C# está Autofac, Ninject y Unity, para Java existe Silk, Guice y PicoContainer, para Swift está Swinject, Cleanse o Typhoon… 

### Ventajas y desventajas

Entre las ventajas de la inyección de dependencias podemos encontrar algunos como el hecho de que **tu código se vuelve más modular y menos acoplado**, ya que los submódulos no necesitan saber detalles de la implementación de otros en tu código. Producto de esta modularidad es que **podemos hacer tests unitarios más fácilmente**, cada parte del programa se puede aislar porque para realizar pruebas no tenemos que proveer a nuestros programas de las referencias reales, sino que podemos fingirlas.

Y una vez configurado, usar la inyección de dependencias puede **hacer que escribas código más rápido** y te quita preocupaciones de tener que instanciar objetos tu mismo.

Es de nuevo importante señalar que no todo son beneficios cuando usamos la inyección de dependencias, usar este patrón también tiene desventajas:  

Comenzar a usarla puede ser tedioso, ya que **necesitas escribir una abstracción de tus servicios** además de su implementación, a veces podrías pensar que escribes código innecesariamente. Y no solo eso, porque luego de eso tienes que configurar tú el inyector. Sin mencionar que, si usas alguna librería, **es muy probable que tengas que ejecutar tu aplicación para verificar que todas las dependencias se resuelvan correctamente** en tiempo de ejecución.

### Código  
En <a href="https://github.com/ThatCSharpGuy/patterns" target="_blank">este enlace</a> puedes encontrar un ejemplo de DI para C#.

