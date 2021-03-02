---
title:  Programación competitiva
date:  2017-06-13 18:00:00
youtube_id: R7fVPM3WRUM
images_folder:  /tv/competitive/
summary:  La programación competitiva enfrenta a programadores que tienen que resolver problemas empleando conocimientos de ciencias computacionales, matemáticas y lógica. Todo esto mediante una sencilla aplicación para computadora.
featured_image:  featured.png
tags:  Meta, YouTube
featured_tag:  Tv
layout: video
---

Sí, hay competencias de programación y son una de las cosas más interesantes del planeta… o al menos eso creo yo.

Pero no se trata de ver quién programa más rápido un carrito de compras o ver quién crea un cliente de Facebook con más funcionalidad. No, las competencias de programación giran en torno a problemas más abstractos. Que involucran lógica, conocimiento de teoría computacional, análisis de algoritmos y matemáticas.

## Los participantes

Una competición de programación usualmente involucra a individuos o equipos que reciben un conjunto de este tipo de problemas para intentar resolverlos

- De manera correcta,
- en el menor tiempo,
- en la menor cantidad de intentos posible,
- respetando los límites dados para su ejecución (temporal y de memoria)

## Los problemas

Los problemas que se intentan resolver deben cumplir con ciertas características, la más importante es que deben estar bien definidos: se debe especificar sus límites, el rango de valores posibles de entrada, así como el formato de esta, y el formato esperado en el que debes presentar el o los resultados.

El problema debe ser resuelto mediante un programa de computadora que sea capaz de tomar datos directamente de la entrada estándar  y enviar la información a la salida estándar, sin presentar ningún tipo de mensaje extra o interfaz gráfica. Esto porque como veremos un poco más adelante: no habrá ningún humano revisando el programa. Este programa debe ser capaz de tomar cualquier valor que se haya definido como una posible entrada en el enunciado del problema, y para cada una de estas entradas funcionar correctamente y presentar un dato de salida.

Así mismo, este programa de computadora que resuelve el problema debe atenerse a ciertas restricciones como la memoria que puede utilizar al ejecutarse o el tiempo que tiene para hacerlo, por ejemplo, una restricción puede ser que un programa no puede ejecutarse durante más de un segundo ni usar más de 150 megabytes de memoria a la vez.

Como ejemplo de un problema:  

<blockquote>
The Kingdom of Byteland has conquered new lands and is willing to use them for profit, due to the danger of being attacked by their arch-rivals: The Agriland Kingdom. For this reason, the Minister of Transport has decided to make an investment to connect all conquered cities by building roads or airports between them.

A trip from city A to city B is possible if either of the following conditions is met:
<ul>
<li>Both city A and B have airports.</li>
<li>Cities A and B are connected by a road or a road sequence.</li>
<li>There is a city X which is reachable from city A and from which there is a path to city B.</li>
</ul>
Building a road between a pair of cities has a cost Pi. Building an airport in a city has a cost C. The Minister of Transport wants to connect all cities and, given the current economic situation, would like to know what is the minimum amount of money the government must spend in order to achieve this goal.
<br />
<a href="http://coj.uci.cu/24h/problem.xhtml?pid=2533" target="_blank">Enlace</a>
</blockquote>

Probablemente algunos de los problemas no parezcan tener relación alguna con programación o ciencia computacional, sin embargo, ahí es en donde debe surgir la capacidad de análisis del o los miembros de un equipo para encontrar le solución para resolver el problema eficaz y eficientemente.

## El juez  

El programa que presentes como solución al problema será revisado por un juez completamente automatizado, al cual debes enviarle el código fuente de tu programa, pero a pesar que envías el código fuente, al juez no le importa para nada cómo lo resolviste: si usaste listas, árboles, magia negra, sumas, restas… al juez lo único que le interesa es que le entregues los resultados correctamente. 

Lo que hace es tratar de compilar el código que le enviaste, y si lo logra toma el ejecutable y lo echa a andar, le introduce conjunto de valores de entrada pre definidos, valores para los cuales ya un conjunto de valores correctos de salida. El juez ya cuenta con esta información antes de iniciar la competencia. Evidentemente, tu como competidor no sabes cuales son estos valores de entrada, y es por eso que tu programa debe funcionar con cualquier entrada que el juez le presente (y que esté definido en el problema). 

El juez juzga por igual a los programas, un buen juez usa cajas de arena, microentornos similares en características que hacen que ninguna solución enviada tenga una ventaja con respecto a otra en términos de velocidad de procesamiento, memoria RAM disponible, etcétera.

## Los posibles resultados
 
Como resultado del jueceo, a cada intento solución le es asignada una calificación, en realidad hay dos calificaciones básicas:

- Accepted o AC, lo cual significa que tu solución fue correcta, o
- Wrong Answer o WA, lo cual significa que tu solución es incorrecta, y hasta ahí

Sin embargo, hay competencias en las que se decide que los jueces den información detallada sobre qué fue lo que salió mal con tu programa, derivado de esto tenemos calificaciones como:

- Presentation error o PE, tu programa produjo resultados correctos, pero no en el formato esperado
- Runtime Error, RE o RTE, tu programa falló durante la ejecución, no falló de se equivocó en una respuesta, sino falló de porque se hizo una división entre cero o se trató de acceder a un bloque de memoria no reservado… 
- Time Limit Exceeded, TL o TLE, tu programa no completó su ejecución en el tiempo asignado para él
- Memory Limit Exceeded, ML o TLE, tu programa usó más memoria de la permitida mientras se ejecutaba… 

## El score  

Existen muchas formas de competencia, pero una de las más famosas y reconocidas es el ICPC en donde generalmente se entrega un conjunto de problemas, entre 9 y 11, y se da un tiempo límite de 5 horas, durante las cuales los equipos tratan de resolverlos. Por cada a cada solución acertada se toma el tiempo en minutos en que la solución fue enviada y se suma al score de dicho equipo. Por cada intento de solución incorrecto se penaliza al equipo sumándole 20 puntos a su score, siempre y cuando al final hayan resuelto dicho problema.

Al final de las 5 horas, gana el equipo con más problemas resueltos, y en caso de empate el ganador será el que tenga menor cantidad de puntos en su score. Esta forma de ordenar a los participantes asegura que <a href="https://icpc.baylor.edu/worldfinals/results" target="_blank">gane el que más problemas resolvió en menor tiempo posible</a>.  

## Los *problemsetters*

Ahora, tal vez te estés preguntando ¿y quién se inventa estos problemas y le da soluciones al juez? Pues verás, estos problemas los establecen personas ya experimentadas en temas de teoría computacional, matemáticas, lógica… pero no solo eso, sino que también deben tener experiencia en programación, ya que, para cada problema propuesto, estos deben tener una solución que funcione bajo las restricciones establecidas por ellos mismos. También, para cada problema deben presentar el conjunto de entradas, cada uno con su correspondiente salida correcta, para que al ser introducidas a los problemas del competidor, puedan compararse contra estas.

## Los lenguajes  

En realidad, puedes resolver problemas en cualquier lenguaje de programación que tenga una forma de leer y escribir datos a los flujos estándar, sin embargo los lenguajes oficiales de las competencias más reconocidas son C, C++, Java y Python (pobre C#)

## Las competencias  

Por mucho, las competencias más famosas son:

- La <a href="https://icpc.baylor.edu/" target="_blank">Competición Internacional Universitaria de Programación de la ACM o ACM ICPC</a> que es una competencia anual entre universidades de al rededor del mundo, para llegar a ella actualmente los equipos de las universidades tiene que pasar por una serie de rondas clasificatorias. Justo acaba de ser la final mundial en donde la  Universidad ITMO de San Petersburgo, Rusia ganó por séptima ocasión en la historia de la competencia. Acércate a <a href="http://campus.acm.org/public/chapters/geo_listing/index.cfm?region=worldwide&type=stu&regions" target="_blank">capítulo estudiantil de ACM</a> para más información.  
- Pero si no eres universitario, para ti existe la <a href="http://www.ioinformatics.org/index.shtml" target="_blank">Olimpiada internacional de Informática o IOI</a>, que es una excelente manera de comenzar a entrenar para desafíos mayores como la ICPC, ya que también se realiza cada año y reúne a competidores de todo el planeta y para llegar a ella también existen rondas clasificatorias. Esta está enfocada a estudiantes de nivel primaria y secundaria, <a href="http://www.ioinformatics.org/a_d_m/members.shtml" target="_blank">revisa si tu país es miembro</a> y ponte en contacto con ellos.
- Pero si no eres ni estudiante, no te preocupes, siempre puedes practicar la programación en jueces en línea, como <a href="http://codeforces.com/" target="_blank">CodeForces</a>, <a href="https://www.codechef.com/" target="_blank">CodeChef</a>, <a href="https://uva.onlinejudge.org/" target="_blank">UVa Online Judge</a>, o las enfocadas en Latinoamérica: La <a href="http://coj.uci.cu/index.xhtml" target="_blank">COJ</a> y <a href="https://omegaup.com/" target="_blank">omegaUp</a>
- Y una vez que has practicado los suficiente, por qué no competir en concursos como <a href="https://code.google.com/codejam/" target="_blank">Google Code Jam</a> y <a href="https://www.facebook.com/hackercup/" target="_blank">Facebook Hacker Cup</a> y ganar un poco de dinero mientras te diviertes.

Si llegaste hasta acá, te cuento que encontré un link a <a href="https://github.com/lnishan/awesome-competitive-programming" target="_blank">este proyecto de GitHub</a> en donde este proyecto de GitHub, en donde se detallan recursos de programación que te ayudarán a comenzar.
