---
layout: post
title: Conectando un Bubble Display a un Netduino Plus 2
date: 2015-07-17 18:31:38
author: Antonio Feregrino
categories: netduino c#
excerpt: Para un proyecto Instrumentación en la escuela decidí desempolvar mi Netduino Plus 2, en el proyecto decesitábamos mostrar las mediciones de tres distintos sensores en un par de displays de 7 segmentos.
lang: es
featured_image: featured.png
alias: /bubble-display-netduino-plus-2/index.html
tags: Netduino
featured_tag: Netduino
---

<p>Para un proyecto Instrumentación en la escuela decidí desempolvar mi Netduino Plus 2, en el proyecto decesitábamos mostrar las mediciones de tres distintos sensores en un par de displays de 7 segmentos, para ello decidí comprar un Bubble Display que no trae 2 sino 4 displays de 7 segmentos en un encapsulado muy estético. Al principio no tenía ni la menor idea de cómo cablearlo, pero consultando el datasheet resultó sencillo.</p>
<h3>Material</h3>
<p>Además del Bubble Display, también usé 7 resistencias de 330 Ω, 4 de 1kΩ y 4 transistores BC557.
El bubble display no necesita explicación, en México lo compré  <a target="_blank" href="http://www.330ohms.com/Display-de-Burbuja--7-Segmentos-4-dgitos_p_345.html">aquí</a>. Las resistencias de 330 son para no dañar el display (se conectan entre este y la tarjeta del Netduino), las resistencias de 1k se usan para la misma razón pero conectado a los transistores mientras que los transistores se emplean como switches que permiten el flujo de la corriente a tierra ya que el display es de cátodo común.</p>

<h3>Programación</h3>
<p>La parte que más me agrada de esto es la programación, para hacer uso del display, cree una clase llamada Bubble7 a través de la cual se pude interactuar con el display y cada uno de sus segmentos. El constructor de la clase recibe como parámetros los pines a los que está conectado display y los transistores.</p>

<p>Expone varios métodos, entre ellos el de display imprimir un dígito <code>PrintDigit</code>, también es importante el método <code>TurnOnDisplay</code> ya que con este se indica qué display estará activo y mostrará datos. Para crear el efecto de que todos los displays están encendidos a la vez, debe existir un retardo muy pequeño entre que se enciende un display mostrando un número y otro, como se muestra en el método <code>ShowNumber</code>. La clase está en <a target="_blank" href="https://gist.github.com/fferegrino/22cda60be970e98c90a1">GitHub</a> para su descarga, acá pongo una muestra de cómo se peuede usar para implementar un contador.</p>

```csharp  
public class Program
{
    public static void Main()
    {
        Bubble7 bubble = new Bubble7(Pins.GPIO_PIN_D0, Pins.GPIO_PIN_D1,
            Pins.GPIO_PIN_D2, Pins.GPIO_PIN_D4, Pins.GPIO_PIN_D3,
            Pins.GPIO_PIN_D6, Pins.GPIO_PIN_D5, Pins.GPIO_PIN_D7,
            Pins.GPIO_PIN_D8, Pins.GPIO_PIN_D9, Pins.GPIO_PIN_D10);

        int count = 0;
        while (true)
        {
            if (count == 9999)
                count = 0;
            bubble.ShowNumber(count++);
        }
    }
}
```  
<h4>Sugerencia de armado</h4>
<blockquote class="imgur-embed-pub" lang="en" data-id="a/qaOH1" data-context="false"><a href="//imgur.com/a/qaOH1">Bubble Display set up</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>