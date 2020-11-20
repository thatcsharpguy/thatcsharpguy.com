layout: post
title: Cosas para hacer en C#: IoT
date: 2016-01-25 19:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: A lo largo de los años se ha avanzado en la creación de un ambiente más amigable para las personas que están interesadas en la programación de microcontroladores para construir sistemas a la medida o a veces solo jugar con ellos.
featured_image: featured.jpg
lang: es
affiliate: true
tweet_id: 691793005305544705
tags: Netduino, AprendeCSharp
featured_tag: AprendeCSharp

Anteriormente la programación de dispositivos electrónicos de bajo nivel estaba "reservada" a un cierto grupo de personas que destinaban horas de su vida para aprender a programar usando lenguaje ensamblador, estudiaban las instrucciones para determinado microcontrolador y luego la pasaban difícil tratando de optimizar memoria aquí y allá para lograr que sus programas funcionaran como deseaban.

Para la suerte de los demás, yo incluído, a lo largo de los años se ha avanzado en la creación de un ambiente más amigable para las personas que están interesadas en la programación de microcontroladores para construir sistemas a la medida (o a veces solo jugar con ellos). Entre los avances más significativos está la posibilidad de escribir código para dichos dispositivos en un lenguaje de alto nivel. Ejemplos hay varios, por decir algunos: Arduino con C y Netduino con C#... y es precisamente de este útlimo del que hablaré en una parte de este post.  
  
## Netduino    
Tal y como suena, Netduino es una plataforma similar al Arduino, ambas son *open source*, con la diferencia de que el Netduino funciona con el **.NET Microframework como plataforma de ejecución**, de los programas escritos para él, lo cual significa que podemos emplear C# para escribir código que se ejecute en él.  
  
Antes de continuar, hay algo que debemos dejar claro, y es que al ser una versión recortada del framework, es posible que no tengamos acceso a algunas de las funciones más complejas que si podemos usar con la versión completa de este, sin embargo no es mucho problema puesto que el acceso a los *namespaces* `System.IO` y `System.Net` sigue existiendo, por lo tanto nuestras capacidades para escribir archivos y conectarnos a internet siguen intactas.  
  
Lo que también sigue intacto es el acceso a los tipos de dato primitivos y la posibilidad de escribir nuestros propios tipos complejos, podemos usar expresiones lambda, tipos anónimos, métodos... en fin, casi todo nuestro conocimiento de C# puede ser empleado al construir un app para el Netduino.   
  
### Anatomía de un programa sencillo 
Si nunca has tenido un acercamiento con la programación de dispositivos, puede que encontrarte con un programa escrito para el Netduino te sea un poco extraño, veamos por ejemplo el *"Hola mundo"* de la programación para dispositivos: Hacer parpadear un led.

```csharp  
public class Blinky
{
    public static void Main()
    {
        OutputPort led = new OutputPort(Pins.ONBOARD_LED, false);

        while (true)
        {
            led.Write(true);
            Thread.Sleep(250);
            led.Write(false);
            Thread.Sleep(250);
        }
    }
}
```  
  
 - Primero que nada: como en cualquier otro programa, todo debe pertenecer a una clase.
 - El método estático `Main` es nuestro punto de entrada
 - Usualmente nuestros programas funcionan dentro de un ciclo infinito. Al principio a mi se hacía la parte más extraña.  
 
#### Entradas y salidas  
Todas las entradas y salidas de nuestros programas son a través de los pines de entrada y salida de la tarjeta de nuestro Netduino, y podemos hacer uso de ellos al "envolverlos" en objetos como el `OutputPort` del ejemplo anterior. El `OutputPort` representa una salida digital, de 1 o 0, es por eso que podemos usar el método `Write` y pasarle un valor booleano. Valor que en este caso encenderá o apagará el led de la tarjeta.

Para otras operaciones tenemos:
  
 - `InputPort` que puede ser usado para recibir un 1 o un 0 de un puerto de entrada  
 - `AnalogInput` que es similar a `InputPort` pero para valores análogos  
 - `InterruptPort` que habilitará un puerto que puede llamar a un evento cuando percibe un cambio de voltaje  
 - `TristatePort` que es un puerto que puede ser usado para lectura o escritura  

Y listo, sabiendo usar los puertos y con todos tus otros componentes de electrónica (resistencias, transistores, amplificadores...) ya tienes todo para empezar a desarrollar tus propios sistemas.  
 
### Shields  
Un momento... es probable que no tengas conocimientos profundos en electrónica (una vez más, como yo) y es en estos casos cuando los *shields* o, escudos, entran en juego.  
  
Los *shields* no son más que módulos que se conectan en nuestro Netduino y extienden su funcionalidad con cosas como WiFi, Bluetooth, control de motores, displays, relevadores y más. Todo sin requerir profundos conocimientos técnicos sobre resistencias y voltajes. La gran ventaja de la tarjeta de Netduino es que muchos de los escudos fabricados para Arduino son compatibles con ella, y es por eso que tenemos acceso a muchas posibilidades.  
 
Muchos de los *shields* vienen acompañados de librerías de código que envuelven la funcionalidad del módulo de hardware en un módulo de software hecho a la medida, y que hacen aún más fácil trabajar con ellos, ahora desde la perspectiva del software. Inclusive algunas de estas librerías son distribuídas a través de paquetes de NuGet.  

### Proyectos  
¿Qué sería de este post si no pusiera ejemplos de cosas que se pueden hacer con Netduino? es por eso que acá te pongo una recopilación de ejemplos:  

#### Automatización de una casa con Netduino  
Este es un proyecto de <a href="http://twitter.com/LogicalDan" rel="nofollow" target="_blank">Dan Thyer</a> en donde <a href="" target="_blank">automatizó una parte de su hogar</a> usando Netduino y Kinect, el proyecto en si es interesante, sin embargo lo que más llama la atención es verlo a él controlar una manguera con tan solo mover su mano:  
<div style="text-align: center;">
    <iframe width="420" height="315" src="https://www.youtube.com/embed/FWINsKcP8oQ" frameborder="0" allowfullscreen></iframe>  
</div>

#### Control de una pecera  
Este proyecto es de alguien que quiso controlar y monitorear su pecera, todo usando componentes fáciles de encontrar y su Netduino Plus 2. Al final integró el monitoreo de la tapa superior, balanceo de PH e iluminación y temperatura. Publicó todo en <a href="https://reefindotnet.codeplex.com" target="_blank" rel="nofollow">Codeplex</a>.

#### Vigilante de puerta   
Y este es un poco más sencillo, hice una aplicación que, junto con Xamarin, <a href="/post/pushing-from-netduino-to-mobile/">envía una notificación cada vez que alguien abría una puerta</a>, para este usé un Netduino 3 WiFi. Si le échas un ojo y te interesa saber más sobre él, no dudes en preguntarme.

### ¿Donde lo consigo?  
Una de las grandes desventajas es la poca distribución que tiene en ciertos países, pero puedes checar si Amazon hace envíos a tu país, los que yo conozco y he usado son estos <a rel="nofollow" href="http://www.amazon.com/gp/product/B009QOYK2U/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B009QOYK2U&linkCode=as2&tag=thcgu-20&linkId=S5Y5YR4FYENG4U5H">Netduino Plus 2</a><img src="http://ir-na.amazon-adsystem.com/e/ir?t=thcgu-20&l=as2&o=1&a=B009QOYK2U" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important; display:inline;" /> y <a rel="nofollow" href="http://www.amazon.com/gp/product/B00WAGY87S/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B00WAGY87S&linkCode=as2&tag=thcgu-20&linkId=E72OE67HNTGC4V3B">Netduino 3 WiFi</a><img src="http://ir-na.amazon-adsystem.com/e/ir?t=thcgu-20&l=as2&o=1&a=B00WAGY87S" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important; display: inline;" />. Si vives en México, una alternativa (aunque un poco cara) es <a href="http://www.mouser.mx/Search/Refine.aspx?Keyword=Netduino" target="_blank" rel="nofollow">Mouser Electronics</a>. Sea cual sea que consigas, este par de libros te pueden servir, esto si están baratos: <a rel="nofollow" href="http://www.amazon.com.mx/gp/product/1449302459/ref=as_li_qf_sp_asin_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1449302459&linkCode=as2&tag=thcgu02-20">Getting Started With Netduino</a><img src="http://ir-mx.amazon-adsystem.com/e/ir?t=thcgu02-20&l=as2&o=34&a=1449302459" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important; display: inline;" /> y <a rel="nofollow" href="http://www.amazon.com.mx/gp/product/B00COVJUGI/ref=as_li_qf_sp_asin_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00COVJUGI&linkCode=as2&tag=thcgu02-20">Getting Started with the Internet of Things: Connecting Sensors and Microcontrollers to the Cloud</a><img src="http://ir-mx.amazon-adsystem.com/e/ir?t=thcgu02-20&l=as2&o=34&a=B00COVJUGI" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important; display: inline;" />.

### Alternativas  
Una alternativa a Netduino que también funciona con el .NET Microframework son las tarjetas de <a href="https://www.ghielectronics.com/catalog/category/34#cat543" rel="nofollow" target="_blank">GHI electronics</a>. Y tal vez hayas oído hablar de Windows 10 IoT que es otra manera de poner nuestras apps en dispositivos... pero no funciona con el NETMF directamente, sino sobre el sistema operativo.