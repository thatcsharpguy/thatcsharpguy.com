---
title:  Patrones de diseño
date:  2017-07-18 18:00:00
youtube_id: NmuRwZ2CYvA
images_folder:  /tv/design/
summary:  Los patrones de diseño son soluciones reusables a problemas comunes que ocurren en el diseño de software.
featured_image:  youtube.jpg
tags:  Meta, Tv
featured_tag:  Tv
layout: video
---

Ya previamente les había hablado sobre algunos patrones, en este caso arquitectónicos, como el <a href="..\mvc" target="_blank">patrón MVC</a> y el <a href="..\mvvm" target="_blank">patrón MVVM</a>, sin embargo, ahora les quiero hablar de los patrones de diseño.

En este contexto vamos a definir a un patrón como una solución a un problema de diseño de software que se presenta comúnmente, al ser un problema común, esta solución debe ser reusable. Esta solución además establece actores claros y bien definidos que participan en ella.

La idea de los patrones de diseño existe desde hace mucho tiempo, sin embargo, no fueron conocidos como tal hasta 1994 que un grupo de escritores conocido como "Gang of Four" compilaron un montón de patrones en un libro llamado <a target="_blank" href="https://www.amazon.com.mx/gp/product/B000SEIBB8/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B000SEIBB8&linkCode=as2&tag=thcgu02-20&linkId=aaa28d530d133b2fe6c4a20c45f2aaef">Design Patterns: Elements of Reusable Object-Oriented Software (Adobe Reader)</a><img src="//ir-mx.amazon-adsystem.com/e/ir?t=thcgu02-20&l=am2&o=34&a=B000SEIBB8" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />, que pueden conseguir en Amazon.

En fin, a partir de este libro se definieron tres categorías de patrones de diseño:

## En la realidad
Si crees que nunca habías trabajado con con patrones de diseño, tal vez quieras repensarlo, puesto que en realidad nosotros ya hacemos uso de estos patrones, muy seguramente sin percatarnos de hacerlo puesto que muchas de estas soluciones ya están muy bien implementadas y bien cimentadas en los frameworks y lenguajes de programación que utilizamos día a día.

Ahora sí, de vuelta a las tres categorías:

## Creacionales  
Estos patrones se encargan de controlar la creación de instancias de objetos. La idea principal de estos es encapsular los tipos de dato que están siendo creados y ocultar cómo es que estas instancias son creadas y combinadas entre sí. Entre estos patrones están:

### Builder Pattern  
O el patrón Constructor. Este se refiere a separar la creación del objeto, del objeto mismo, delegándole la tarea a otro, el constructor. El objeto constructor se encargará de construir paso por paso el objeto, de acuerdo a lo que nosotros le indiquemos. 

### Factory Method Pattern  
El patrón método fábrica, como su nombre lo indica, es un método dentro de una clase que se encargará de crear instancias de clases, basándose en los parámetros que este recibe. 

### Singleton Pattern  
Este patrón es quizá uno de los más enseñados (o al menos del que más me acuerdo de mis clases en la universidad), este nos ayuda para cuando solo debe existir sola instancia de un objeto durante toda la ejecución del programa.  

### Prototype Pattern
Para cuando necesitamos instanciar una clase a partir de otra, como su nombre lo indica: existe un prototipo de objeto que se clona para generar nuevas instancias de este.

## Estructurales  
Estos patrones lidian con cómo es que los objetos se pueden combinar para crear módulos más robustos de software.

### Adapter Pattern
Tal y como suena, consiste en colocar una capa intermedia, un adaptador, que permita la comunicación entre dos objetos. El mejor ejemplo de esto es la clase `BaseAdapter` cuando desarrollamos para Android, esta clase y sus derivados permiten la comunicación entre la fuente de datos y un `ListView`. 

### Decorator Pattern
Este nos ayuda para añadir funcionalidades nuevas o comportamientos extras a un objeto, sin necesidad de crear sub clases de clases que actualmente ya tenemos. Un ejemplo de esto en .NET son los `Streams` en donde un `Stream` de datos se puede decorar con otro para extender sus capacidades.

### Facade Pattern (Fachada)
Este permite agrupar funcionalidades a través de una sola interfaz, facilitando así su uso por el desarrollador. 

### Composite Pattern
Aplicar este patrón significa que podemos crear una jerarquía de objetos y que su comportamiento seguirá siendo el mismo. Un buen ejemplo de este es cuando desarrollamos con Xamarin.Forms y metemos controles dentro de un `StackLayout`, cada control se renderizará por separado, se establece una relación jerárquica pero su comportamiento base se mantiene.

## De comportamiento
Los patrones de comportamiento nos ayudan a solucionar problemas de comunicación entre objetos.

### Command pattern  
Este patrón introduce un objeto como mensaje de comunicación entre otro par de objetos. Esto ayuda a que el que va a mandar el mensaje conozca poco o nada de aquél que lo va a recibir. Este patrón es muy usado en Windows 10 o Xamarin.Forms.

### Iterator pattern
Este patrón nos ayuda a acceder a los elementos de un conjunto de datos de forma secuencial sin tener que conocer su estructura interna. El mejor ejemplo de este patrón son las colecciones en C#.

### Memento Pattern
Este patrón ayuda a capturar y almacenar el estado de un objeto, de tal modo que este estado pueda ser restaurado más adelante. Las implementaciones más comunes convertirán un objeto a XML o JSON.

### Observer pattern
Este patrón nos permite establecer una relación de publicador-suscriptor entre dos objetos, una implementación de este patrón son los manejadores de eventos en C#.

### Chain of responsibility pattern
Este patrón ayuda a establecer una cadena de responsabilidades, en las que el flujo de un programa viaja de un objeto a otro según se hayan establecido las responsabilidades.

### Strategy pattern 
Este patrón indica que podemos establecer un grupo de algoritmos con entradas y salidas similares, representando estas entradas y salidas a través de una interfaz, ofreciendo así la posibilidad de intercambiarlos.  

## Para terminar  
Estos temas son tan abstractos que cuesta explicarlos en tan poco espacio. Y sé que sin un ejemplo de código es un poco más complicado, así que si quieren saber más a detalle sobre alguno déjenlo acá en los comentarios y les respondo, igual alcanza como para hacer un video sobre eso. Pero por mientras pueden consultar los enlaces en este post para saber más:  

 - <a href="http://www.dofactory.com/net/design-patterns" target="_blank">dofactory</a>: buen sitio sobre la implementación de los patrones en C#.  
 - <a href="https://www.tutorialspoint.com/design_pattern/design_pattern_overview.htm" target="_blank">tutorialspoint</a>: una vista rápida a los patrones de diseño  
 - <a href="https://sourcemaking.com/design_patterns" target="_blank">Source Making</a>: teoría sobre los patrones de diseño.  
 - <a href="https://stackoverflow.com/a/3252646/605482" target="_blank">¿Qué patrones se usan en .NET?</a> en Stack Overflow.  
