---
title:  ¿Qué es MapReduce?
date:  2018-02-19 18:00:00
youtube_id: b27hWyhq9oo
summary:  La idea de MapReduce es ofrecer una forma simple, rápida, escalable y resistente a fallos para manipular enormes cantidades de datos.
tweet_id:  965987034518163457
lang:  en
tags:  Data science, Meta, YouTube
featured_tag:  Tv
layout: video
---

Es un modelo de programación fuertemente orientado a la ejecución paralela y distribuida entre múltiples computadoras, que se utiliza para trabajar con grandes colecciones de datos, digamos, de unos cuantos terabytes (o petabytes). La idea de MapReduce es ofrecer una forma simple, rápida, escalable y resistente a fallos para manipular enormes cantidades de datos. En la terminología de MapReduce a estas manipulaciones se les conoce como trabajos, y así me referiré a ellos de ahora en adelante.

MapReduce surgió de Google y fue presentado en 2004 por dos investigadores de Google, quienes lo publicaron en un paper y fue a partir de ahí que se comenzó a desarrollar en el mundo del open source a partir de ahí se generó una propuesta open source que funciona dentro de Hadoop (que por cierto es otra maravilla del Open Source originada en Google, de la cual si quieren les hablo en otro video). La realidad es que la explicación que voy a dar aquí es muy, muy básica, y los invito a leer el paper y a buscar más información y desde luego, a dejar sus preguntas en los comentarios, no es que yo pueda responderlas, pero seguro yo se las puedo preguntar a alguien más.

## Composición 
Su nombre proviene de dos viejos conocidos de la programación funcional: las funciones Map y Reduce. Como su nombre lo indica,  se compone de dos etapas (en realidad hay tres, pero la tercera es transparente para el desarrollador): la etapa de Map o Mapeo y la etapa de Reduce o Reducción, un poco más adelante les hablaré a detalle de estas etapas.

Como lo mencioné antes, MapReduce fue creado para ejecutarse en múltiples computadoras a la vez, distribuyendo así la carga del trabajo en ellas, cabe destacar que estas computadoras no tienen que ser supercomputadoras carísimas, sino que pueden ser computadoras simples y relativamente baratas, es por eso que cuando se habla de MapReduce, se habla de clusters de computadoras.

Todas las computadoras que participan en un trabajo de MapReduce   contienen un agente que coordina sus recursos, conocido como el NodeManager, este NodeManager a su vez se comunica con el encargado de orquestar todo el trabajo, que es nada más y nada menos que otra computadora dentro del cluster, que contiene un programa llamado ResourceManager, encargado de orquestar toda la operación de un trabajo de MapReduce (este asigna los trabajos, se mantiene al tanto de el estado de cada uno de los NodeManagers y resigna un trabajo no completado si es que alguno de los nodos deja de responder).

## Entradas y salidas. 
Como igual mencioné brevemente al inicio del video, un trabajo de MapReduce tiene como objetivo el tomar una cantidad de datos (de un archivo, de una base de datos o cualquier otra fuente de datos), manipularla de alguna manera y entregar un resultado al final. Pese a lo que indica su nombre "reduce", el archivo generado no necesariamente tiene que ser una versión reducida de la entrada, puede llegar a ser más grande.  

Su funcionamiento es más o menos el siguiente:

### Mapping
De tu entrada de datos tomas un conjunto de información que tiene la forma de pares llave-valor, dentro de la fase del mapper realizas alguna especie de procesamiento sobre ellos, como podría ser separar una cadena por espacios en blanco, encontrar todos los links que contiene un documento web. La idea es que del mapper generes un nuevo conjunto de datos, de nuevo en la forma de llave-valor, aquí cabe señalar que no es necesario que las llaves ni los valores sean los mismos que en los archivos de entrada, pueden ser totalmente diferentes.

### Shuffling
A la salida de los mappers se ejecuta otra etapa, conocida como shuffling, que se encarga de ordenar los datos generados de acuerdo a la llave que tu les asignaste en el mapper. Una vez que se ejecutó la fase de shuffling, los datos están listos para ser consumidos por los reducers.

### Reducing
La tarea de los reducers comienza al ir a recuperar la información de los mappers, cada reducer tendrá la tarea de procesar todos los datos asignados a una sola llave a la vez. En el Reducer tenemos la oportunidad de realizar otro procesamiento sobre los valores, pero ahora con la certeza de que toda la información que tenemos está identificada con una sola llave.  

El reducer, al igual que el mapper, tiene la tarea de generar un nuevo conjunto de información en la forma llave-valor, la cual es depositada nuevamente en un archivo.

## Claves 

Sí, sé que ese asunto de las llaves-valor puede sonar complicado al inicio, pero después de haber implementado una versión muy básica del primer PageRank de Google en MapReduce (dejo el link al post en la descripción) puedo identificar algunas claves:  
 
 - Pensar en forma paralela, y tratar de olvidar temporalmente todos los otros paradigmas que tengas en mente.
 - Que al momento de trabajar en los mappers no vas a tener toda la información que necesitas en un solo lugar, ve a los mappers como una etapa de preparación de la información.
 - Que si quieres que algo se ejecute en un solo lugar, eso sería del lado de los reducers, y para que esto suceda todos los elementos que quieres en un lugar deben tener la misma llave.

## El Hola Mundo
El hola mundo del MapReduce es un ejemplo de conteo de palabras que te voy a tratar de explicar ahora mismo. Imagina que tienes el siguiente texto: 

```
Cuando cuentes cuentos
Cuenta cuantos cuentos cuentas
Por que si no cuentas cuantos cuentos cuentas 
Nunca sabrás cuantos cuentos cuentas
```

Una forma de contar las palabras usando MapReduce es la siguiente:

Los mappers reciben fragmentos del texto, separados por lineas, la información es recibida de la siguiente manera, cada linea es un valor, y la llave de ese valor es el número de línea dentro del archivo. Dentro del mapper, tomamos cada línea y la separamos por espacios, tomamos cada una de esas palabras como llaves y les asociamos como valor un uno y los escribimos a la salida.

La tarea del shuffler será ordenar esta información por la llave , algo así:

Después, esta información es obtenida por los reducers, recuerda que los reducers tomarán todos los elementos asociados con una sola llave y los procesarán dentro de ellos.

En el reducer, se ejecuta un proceso que toma todos los números asociados a una palabra y los suma, tan solo para al final escribir a la salida otro par llave valor, en donde la llave es la palabra y el valor es el número de veces que aparece dicha palabra en el archivo.


## Usos y limitaciones
Por regla general se usa MapReduce cuando queremos afrontar problemas relacionados con grandes, enormes cantidades de datos. Y es por eso que se considera que debe correr en un sistema de archivos distribuidos, como es el caso de HDFS (Hadoop Distributed File System). 

Si bien MapReduce es una propuesta para paralizar y distribuir el cómputo sobre grandes cantidades de datos, tenemos que tener en cuenta que no todos los problemas son susceptibles de afrontarse usándolo, y tampoco es una solución buena para trabajos que requieren de respuesta en tiempo real, MapReduce es más bien para procesamiento offline de información.