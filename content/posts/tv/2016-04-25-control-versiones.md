---
title:  Control de versiones
date:  2016-04-25 18:00:00
youtube_id: Uts65gTG1k0
images_folder:  /tv/control-versiones/
categories:  c-sharp
summary:  El control de versiones es una forma de organizar los elementos de una *cosa* cuyo contenido es modificado con el paso del tiempo.
featured_image:  featured.png
tags:  Meta, Tv
featured_tag:  Tv
layout: video
---

El control de versiones es una forma de organizar los elementos de una *cosa* cuyo contenido es modificado con el paso del tiempo, y digo cosa porque realmente se puede aplicar el control de versiones a cualquier cosa, al menos digital. Inclusive si has hecho algo así:  

<img src="https://thatcsharpguy.github.io/postimages/tv__control-versiones__prehistoria.png" title="Control de versiones rudimentario" />
 
Con un documento o una imagen que estés editando, has implementado el concepto de control de versiones... muy rudimentario, pero lo has hecho.

## Sistema de control de versiones   
Los sistemas de control de versiones son herramientas que nos ayudan a llevar el control de cambios que sufren los componentes de un programa.  

Este tipo de sistemas poseen una especie de repositorio de datos en la que se registra quién modificó qué cosa, cuándo lo hizo y a veces hasta por qué lo hizo, de tal manera que existe un historial de cambios que sea fácil de consultar. 

Además, es posible separar el desarrollo de nuevas funciones (y la probable inestabilidad que estas representan) del código que funciona. 

También hay algunos que permiten destacar un determinado punto en el tiempo y tomar una *versión* de cómo estaba el código en ese momento y marcarla con alguna etiqueta especial, por ejemplo: "primera versión" o "antes de migración".

## Qué se puede almacenar en el control de versiones
Para el desarrollo de una aplicación siempre es necesario que exista código fuente, y es precisamente este el candidato ideal para ser introducido en el control de versiones, sin embargo, no solo podemos poner código en este tipo de sistemas, también se pueden meter archivos de configuración, binarios, imágenes y otros recursos necesarios para la aplicación que estamos desarrollando.  

## Terminología común
A pesar de que hay muchos sistemas de control de versiones, muchos comparten conceptos que son importantes conocer:   

Comenzando por el **repositorio**, que es el lugar dentro de la computadora donde se guardan los archivos de la aplicación, los usuarios obtienen una copia de los archivos de ese repositorio y la colocan dentro de un **espacio de trabajo**. Como ya mencioné antes, una **versión** es una captura de todos los elementos en un determinado punto del tiempo, es probable que algunas de las versiones sean **etiquetadas** de alguna manera especial para señalar su importancia.

En este tipo de sistemas se avanza entre las versiones mediante **cambios**, que no son más que las modificaciones que hacemos a los archivos dentro del control de versiones. Los cambios que hemos hecho nosotros se registran en el repositorio a través de una **publicación** y para obtener los cambios que otras personas han hecho es necesaria una **actualización**.

Ocasionalmente, y con mayor frecuencia cuando pertenecemos a un grupo grande de desarrolladores, pasa que dos o más modifican las mismas líneas de un archivo fuente, y es cuando ocurre un **conflicto**. Cuando ocurre un conflicto, es necesario que alguien decida cuál es la mejor forma en la que el archivo seguirá funcionando, a esta tarea se le conoce como **resolución de conflictos**. Es importante decir que algunos sistemas tienen  integrado un módulo de resolución de conflictos que actúa cuando los conflictos a resolver no son muy complejos.

Usando los sistemas de control de versiones es posible separar distintas etapas del desarrollo, por ejemplo: supongamos que no queremos tocar el código de producción para implementar una nueva funcionalidad, es en estas ocasiones cuando podemos hacer una **abrir una rama**, que es como *crear* una copia de los archivos que contiene el repositorio para trabajar con ellos. Posteriormente, cuando volvemos a unificar los archivos que separamos con la apertura de las ramas, debemos hacer una **fusión** que tomará nuestros cambios y los publicará en la rama destino.  

## Categorías
Es importante conocer algunas de las categorías en las que podemos clasificar los sistemas de control de versiones, yo les hablaré de dos formas de clasificación.

La primera es **por la forma en la que se almacena el código fuente**:

 - **Centralizado**, de este modo existe un solo repositorio donde reside el código *maestro*. En este modo centralizado existe una referencia clara de la versión en la que la aplicación está. Aunque cabe señalar que debe existir un usuario, o un grupo de ellos, responsable que autorice algunas acciones sobre el repositorio, como crear una versión o abrir una rama.
 - **Distribuidos**, al contrario del centralizado no existe un código *maestro* puesto que cada usuario puede tener un repositorio del cual es responsable, lo cual otorga libertad. pero sacrifica un poco de control sobre las versiones. En estos casos usual que se utilice un servidor de referencia, como en caso del modo centralizado.

La segunda es por el modo en el que los usuarios del sistema contribuyen en los cambios de los archivos, hay dos modos:  

- **Exclusivo**, de este modo es necesario que el usuario le indique al repositorio qué archivos va a modificar para que el repositorio los bloquee y nadie más pueda trabajar en ellos hasta que estos sean liberados por quien inicialmente los  solicitó. ¿Recuerdan que hace poco hablé de los conflictos? … pues en este modo de colaboración desaparecen, simplemente no se da el caso en el que dos usuarios modificaron el mismo archivo porque el sistema no se los permite, aunque esto puede generar cuellos de botella en el ciclo de desarrollo.

- **Colaborativo**, de este modo no es necesario informar a nadie qué archivos vamos a modificar, aunque te recomiendo que sí le avises a tus compañeros de equipo, porque es precisamente bajo este modo cuando aparecen los ya famosos conflictos. Si la coordinación entre compañeros es la adecuada es posible agilizar el ciclo de desarrollo.  

## Ejemplos de sistemas de control de versiones

- <a href="https://es.wikipedia.org/wiki/Microsoft_Visual_SourceSafe" target="_blank" rel="nofollow">SourceSafe</a>, que ya está en desuso, pero era uno de los mejores exponentes del modo de contribución exclusivo
- <a href="https://www.mercurial-scm.org" target="_blank" rel="nofollow">Mercurial</a>, que es un sistema distribuido
- <a href="https://msdn.microsoft.com/en-us/library/ms181237.aspx" target="_blank" rel="nofollow">Team Foundation Version Control</a>, que es uno de los máximos exponentes del modo centralizado
- Y mi favorito personal, <a href="https://git-scm.com" target="_blank" rel="nofollow">Git</a>, que es centralizado y colaborativo.
