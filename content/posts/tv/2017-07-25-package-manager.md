---
title:  Gestores de paquetes
date:  2017-07-25 18:00:00
youtube_id: tiifSYUCwQA
images_folder:  /tv/package/
summary:  Los gestores de paquete le otorgan a los desarrolladores una manera más sencilla de reutilizar el código creado por otros, al manejar la instalación, actualización y remoción de bibliotecas de software.
featured_image:  package.jpg
tags:  Meta, Tv
featured_tag:  Tv
layout: video
---

Los gestores de paquetes son esas herramientas sin las cuales el desarrollo actual sería un gran dolor de cabeza. Y es que últimamente son el pan de cada día de los desarrolladores, especialmente si se está trabajando con tecnologías "modernas" como el desarrollo móvil o web. 

Estos no nacieron inicialmente como una herramienta netamente enfocada al desarrollo de software, sino como una herramienta para añadirle funcionalidad a los sistemas operativos basados en Linux, si has usado alguna distro de Linux tal vez te suene familiar las palabras "apt", o "yum", pues bien, esos son gestores de paquetes.

Esta idea después migró al código, otorgándole a los desarrolladores una manera más sencilla de reutilizar el código creado por otros al manejar la instalación, actualización y remoción de bibliotecas de software, de hecho desde 1995 en que CPAN (un archivo de módulos para el lenguaje PERL) fue puesto en línea, es que se tiene la primera noción de un gestor de paquetes para el desarrollo.

Los gestores de paquetes tienen muchos beneficios, pero dos de los que hay que destacar son los siguientes:

- Los gestores de paquetes reducen la cantidad de código de la que tienes que hacerte cargo: bueno, esto es solo una consecuencia de usar código de terceros, sin embargo, los gestores de paquetes suelen encargarse de más tareas que las de simplemente descargar una pieza de software, hay algunos que inclusive la integran a tu código añadiendo archivos auxiliares o inclusive modificando la configuración de tu proyecto.

- Hacen más sencilla la tarea de añadir y gestionar las bibliotecas a tu proyecto, y es que tú ya no estás a cargo de instalar las bibliotecas. Encima de todo se encargan también resolver otras dependencias que estas bibliotecas que estás instalando tengan, así podría parecer que tu instalas solo un paquete, cuando en realidad el gestor está instalando otros tres   sin que tú te tengas que preocupar por ellos. A esto se le llama gestión de dependencias.

- Además, muchos de ellos son compatibles con el versionamiento semátnico, que como ya vimos en un video pasado, tiene muchas ventajas a la hora de desarrollar bibliotecas que cuentan con una API.

En la actualidad es muy sencillo para todos crear una biblioteca de código y hacerla disponible a todo el mundo a través de los gestores de paquetes. Bastaría con que empaquetaras tu código de una forma en la que el gestor la entienda y lo pongas en un lugar accesible como un servidor en internet, de hecho, muchos de los gestores de paquetes ofrecen servicio de alojamiento para que los desarrolladores coloquen ahí lo que desarrollen. 

Pero no te preocupes, que usar un gestor de paquetes no es obligatorio, puesto que siempre tendrás la opción de descargar la biblioteca que deseas usar y compilarla tu mismo (si es posible) y luego instalarla a mano en tu proyecto, aunque esto no suene tan atractivo después de hacerlo varias veces. 

Para tu lenguaje o entorno seguramente existen los gestores de paquetes, con el que yo estoy más familiarizado es con NuGet o *Nu-jeh* como dice Miguel de Icaza, aunque también existe Packet, si lo tuyo es Xcode ahí tienes CocoaPods, Composer si rabajas con PCP, gopm para GO, en fin, <a href="https://github.com/showcases/package-managers" target="_blank">en este enlace</a> podrás encontrar muchos más gestores de paquetes.