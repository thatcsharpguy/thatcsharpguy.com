---
title:  Los Jupyter Notebooks
date:  2017-12-11 18:00:00
youtube_id: xcXky3PxVHA
images_folder:  /tv/jupyter/
summary:  Los Jupyter Notebooks son una herramienta bastate útil para documentar el código antes de llevarlo a producción. ¡Y no son solo para Python!
featured_image:  featured.png
tweet_id:  940237219826229248
lang:  en
tags:  Data science, Meta, YouTube
featured_tag:  Tv
layout: video
---

Los Notebooks de Jupyter son unas hojas de código interactivas...

El código ya es interactivo por si mismo, puedes modificarlo, ejecutarlo a tu antojo... pero el tipo de interactividad que ofrecen los Jupyter Notebooks es algo distinto, pero vamos a ver algo de historia...  

La idea detrás de estos Notebooks proviene de IPython que es un proyecto orientado a ofrecer una consola interactiva para trabajar con el lenguaje de programación Python (este proyecto fue iniciado por Fernando Pérez, un físico desarrollador de software colombiano), el proyecto, que ya era de por si interesante, tuvo una adición importantísima en 2011 con la aparición de los IPython Notebooks (ahora llamados Jupyter Notebooks) para convertirse en un entorno con el cual podemos interactuar a través de un navegador. Básicamente la idea era ofrecer un entorno para facilitar la tarea de la programación orientada al ámbito científico, sin embargo, también tiene un uso genérico con gran potencial. Espero veas por qué con esta rápida introducción.

Permite trabajar con gráficas, ecuaciones, videos, imágenes, simuladores y desde luego, código. Todo... combinado. Imagina: combinar las características de un entorno simple de programación (como el *syntax highlighting* y auto-completado) con la habilidad de poder escribir texto enriquecido junto al código. La idea es que sirva para documentar lo más posible alguna tarea que estamos realizando, tan solo para después tomar solo el código y llevarlo a una aplicación productiva. Además ofrece posibilidades para que cada uno de estos Notebooks sea evaluado automáticamente, lo cual también los hace ideales para hacer prácticas escolares, y de hecho, en la universidad ya he tenido ya varias prácticas así.

Los Notebooks se crean con la Notebook App, que es una aplicación que está compuesta por dos componentes en una arquitectura cliente-servidor. 

La parte del cliente es el editor de código con el cual creamos y modificamos los Notebooks; este editor basado en HTML, CSS y JavaScript es el que usamos a través de un navegador web, aquí es donde escribimos el código y vemos los resultados de ejecutarlo. 

Mientras que el servidor se encarga de ejecutar el código que nosotros introducimos en los Notebooks y devolver los resultados al cliente. Este servidor es una especie de servidor web que a cada una de estas hojas interactivas les asigna un hilo de ejecución dentro de la computadora en la que está corriendo, y es en realidad dentro de este hilo de ejecución en donde se ejecuta nuestro código. El cliente y el servidor se comunican a través de peticiones HTTP y WebSockets.

Al ser de código abierto (y al tener una gran comunidad detrás) tiene muchas posibilidades de ser expandido mediante Widgets que   los dotan de mayor funcionalidad. Entre estos Widgets están algunos que permiten trabajar con información geoespacial, video y audio, crear  tablas paginadas o crear una "interfaz amigable" para modificar el código. Y es también gracias a la comunidad que soporta diversos lenguajes de programación aparte de los con que fue creado en mente: Julia, Python y R. Inclusive se puede utilizar junto con C#... aunque el soporte todavía no es muy bueno.

Si eres fan de C#, existe algo muy parecido pero lamentablemente no tiene tanta flexibilidad como los Notebooks de Jupyter, se llaman los Xamarin Workbooks, escribí un post sobre ellos, dejo el enlace en la descripción.

Pero bueno, si quieres comenzar a usar los Notebooks pero eres un novato como yo, mi recomendación es que instales [Anaconda](http://jupyter.readthedocs.io/en/latest/install.html#id3), que es otra gran herramienta, pero de la cual por el momento solo nos interesa usar los Notebooks. En la siguiente parte de este video les mostraré en un Notebook en acción.