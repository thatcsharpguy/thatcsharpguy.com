---
layout: post
title: Mi setup de Python en Windows
date: 2019-08-12 19:00:01
author: Antonio Feregrino
summary: Una parte de mi sigue atado a este sistema operativo, pero así consigo mi dosis de Python en Windows.
lang: es
tags: Python, Windows
featured_tag: Python
---

A pesar de que últimamente he estado utilizando cada vez más plataformas Linux, una parte de mi sigue atado a Visual Studio y Windows; sin embargo, no dejo que esto me detenga para hacer desarrollo en Python casi como lo hago en Ubuntu.  

Es por eso por lo que decidí hacer esta **simple** guía, para aquellos que están interesados en usar Python con Windows y mantener un poco de control sobre como configurarlo. Además de que estoy seguro de que voy a olvidar como instalar Python para cuando formatee mi computadora, así que esta también es una guía para el yo del futuro.  

## Chocolatey  

Chocolatey es *"un gestor de paquetes para Windows (como apt-get or yum pero para Windows)"*, y lo voy a usar para instalar `git` y `make`. Yo uso Git para control de versiones y Make para *"automatizar"* las tareas un poco tediosas.  

Para instalar Chocolatey, inicia una consola de PowerShell como administrador y ejecuta el siguiente comando:  

```shell  
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```  

¡Ten cuidado con los comandos que copies y pegues en tu consola de PS! [revisa el comando anterior aquí](https://chocolatey.org/docs/installation#install-with-powershellexe) solo para confirmar.  

### Instalando las herramientas  

Una vez que Chocolatey ha sido instalado, cierra la consola de PS e iníciala de nuevo como administrador. Copia y pega el siguiente comando para instalar `make` y `git`:  

```shell  
choco install -y make git
```  

La `-y` en medio del comando es porque no quiero que *choco* me pida confirmación... yo sé lo que estoy haciéndo.  

Una vez que la instalación termine, podrás ejecutar `make` en PS y ver algo como lo siguiente:  

```text
make: *** No targets specified and no makefile found.  Stop.
```

## Python 

Para instalar Python debes dirigirte a las [descargas de Python](https://www.python.org/downloads/), puedes elegir la versión que quieras; yo elegí [Python 3.6.8](https://www.python.org/downloads/release/python-368/), y descargué el ejecutable llamado *"Windows x86-64 executable installer"*.  

Cuando lo ejecutes, verás una ventana como la siguiente:  

![](https://res.cloudinary.com/practicaldev/image/fetch/s--fpSAGdA4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Isdjhzd.png)  
  
En este caso, seleccioné *Install Now*, y me aseguré de tomar nota de la ruta mostrada ahí. Una vez que la instalación terminó, tuve que editar las variables de entorno para asegurarme que apuntaran hacia donde Python fue instalado, para hacer esto es necesario modificar la variable `Path` de la manera siguiente:  

![](https://res.cloudinary.com/practicaldev/image/fetch/s--c8reUa3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1WstUpH.png)  

Los nuevos valores son:  

 - `%USERPROFILE%\AppData\Local\Programs\Python\Python36`  
 - `%USERPROFILE%\AppData\Local\Programs\Python\Python36\Scripts` 

Debes asegurarte de que el valor de la ruta que termina en `WindowsApps` quede debajo de las nuevas rutas.  

Ahora debes poder usar Python desde la terminal. Escribe `python` y deberías ver algo similar a lo siguiente: 

```text  
Python 3.6.8 (tags/v3.6.8:3c6b436a57, Dec 24 2018, 00:16:47) [MSC v.1916 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
```

### `pipenv`  

Una vez que ya tienes Python instalado, ya casi estamos por terminar. Sin embargo, para mantener una instalación limpia, y aislar cada una de nuestas distintos proyectos es recomendable usar un gestor de [entornos virtuales](https://www.youtube.com/watch?v=GM-RcOaGN4w), como *Pipenv*. Para instalarlo, escribe el siguiente comando en la terminal:  

```shell script  
pip install --user pipenv
```  

Esto instalará `pipenv`, pero aún estamos a un paso de poder usarlo. Como usamos la opción `--user`, el paquete se instaló en una ubicación que necestamos añadir a nuestra variable `Path`, de manera similar a como lo hicimos cuando instalamos Python.  

Las rutas por añadir son:  

 - `%USERPROFILE%\AppData\Roaming\Python\Python36`  
 - `%USERPROFILE%\AppData\Roaming\Python\Python36\Scripts` 

Asegúrate que estas rutas queden sobre las variables de Python que recién añadimos.  

## Herramientas adicionales  

Yo uso otras herramientas que te recomiendo instalar, sin embargo, estas podrán no ser tan relevantes para ti:  

 - [Visual Studio](https://visualstudio.microsoft.com/) 
 - [Visual Studio Code](https://code.visualstudio.com/)
 - [Notepad++](https://notepad-plus-plus.org/)  
 - [MobaXterm](https://mobaxterm.mobatek.net/) 
 - [Fiddler](https://www.telerik.com/fiddler)
 - [7-zip](https://www.7-zip.org/download.html)

Espero que este post the haya sido de utilidad, y si tienes dudas o comentarios, me puedes preguntar en [Twitter](https://twitter.com/io_exception). 

[Here is the same post but in english](https://www.youtube.com/watch?v=UvaZZi_zgSo&t=186s), it would be really nice if you can visit there too.
