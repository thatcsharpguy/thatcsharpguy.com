---
title:  Programando con estilo
date:  2016-08-10 18:00:00
youtube:  Htsknf4L2o4
images_folder:  /tv/estilo/
summary:  Programar con estilo es escribir nuestros programas fáciles de entender, bien documentados y siguiendo convenciones de escritura previamente adoptadas.
featured_image:  featured.jpg
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

Y no, el título no se refiero a escribir código con traje sastre, los meñiques levantados mientras tomamos té. Sino escribir nuestros programas fáciles de entender, bien documentados y siguiendo convenciones de escritura previamente adoptadas. Esto hará que nuestro código sea universal.  

Sí, cada programador es único y tiene su manera de hacer las cosas, sin embargo esto se puede convertir en un gran problema cuando se tenga que optimizar, corregir o ampliar el código de una aplicación. En el mejor de los casos recordarás para qué sirve esa variable llamada **asdflkj** que escribiste hace meses… en el peor de los casos no serás tu quién tenga que hacerlo.

O tal vez esa sea tu intención: "si fue dificil de escribir, que sea difícil de entender". Pero esperemos que no.  

## Parte del problema
  
En mi opinión, parte del problema viene desde las escuelas tradicionales, al menos en la que <a href="http://blog.fferegrino.org/no-me-siento-ingeniero/" target="_blank">yo estudié</a>, cada semestre eran proyectos nuevos para cada asignatura, entonces ni te preocupabas por tener que hacer las cosas mantenibles, con que el "sistema" funcionara al final del semestre bastaba.  

Creo que no se nos inculca la habilidad de trabajar sobre una sola cosa y darle mantenimiento con el tiempo, lo cual nos forzaría (o al menos nos indicaría que) debemos hacer las cosas bien desde el inicio. Para mi fue una gran sorpresa el día que en el trabajo tuve que regresar a modificar el código que había escrito apenas unos meses atrás. Como una película de terror.  

## Una posible solución  

Como uno de los esfuerzos por combatir estas malas prácticas en cuanto a la escritura del código es que surgieron las **guías de estilo**, que no son mas que especie de "reglas" para nombrar nuestras variables, métodos o funciones, clases… y hasta algunas de las más celebres batallas como usar tabs o espacios o poner el corchete de apertura en la misma línea o no.  
 
No existe una única guía de estilo para cada lenguaje, sino que pueden existir varias, dependiendo del proyecto o la organización que las adopte. Por ejemplo, existen las <a href="https://msdn.microsoft.com/en-us/library/ff926074.aspx" target="_blank">*Coding conventions* de C#</a> que son recomendadas por Microsoft para el lenguaje, luego también existe la  <a href="https://github.com/dotnet/corefx/blob/master/Documentation/coding-guidelines/coding-style.md" target="_blank">guía de estilo de la fundación .NET</a>, pero también existen las <a href="https://github.com/MonoGame/MonoGame/wiki/Coding-Guidelines" target="_blank">Coding guidelines para el proyecto MonoGame</a>.  

Seguramente para el lenguaje de tu preferencia o con el que estés trabajando actualmente también existen estas guías de estilo. Y si no existe o sientes que ninguna se adecua a tus necesidades (o las de tu organización), no te preocupes: si bien lo más recomendable es que sigas alguna guía existente, desarrollar la tuya propia y aplicarla en el código. 

Ya sea que encuentres una guía o crees una propia, el paso siguiente **aplicarla consistentemente**, de nada servirá si usas una un día y al otro la cambias. El uso de las guías de estilo es una de las cosas que hacen más sencilla la colaboración en el código, <a href="../control-versiones" target="_blank">además de los sistemas de control de versiones</a>. 

Y ahora comienzen a programar con estilo si es que aún no lo hacen. Nos vemos la próxima.