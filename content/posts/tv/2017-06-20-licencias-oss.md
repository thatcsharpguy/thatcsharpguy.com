---
title:  Licencias de software libre
date:  2017-06-20 18:00:00
youtube:  sDchhCV_BKc
images_folder:  /tv/licencias/
summary:  Las licencias de código libre son lo que en realidad hace al código libre, ya que establecen las condiciones y lineamientos bajo los cuales el código que protegen puede ser compartido.
featured_image:  featured.jpg
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

Antes de comenzar quisiera decirles que de ninguna manera tengo la intención ni estoy capacitado para dar asistencia legal. Este video es con fines informativos nada más.

Seguramente en algún momento de su vida se han encontrado, o encontrarán código en internet desprotegido y desamparado… fácilmente accesible para quién sea que lo agarre, pero, ¿esto significa que lo puedes tomar...? no, primero debes fijarte en la licencia.

Toma por ejemplo GitHub, uno de los sitios por excelencia en donde el código es compartido libremente, si ahí encuentras algo que te podría resultar útil primero deberías revisar la licencia que lo acompaña para que sepas qué es lo que puedes hacer y qué es lo que no puedes hacer con él.

Antes de comenzar a ver las otras licencias, debes tener en cuenta que, si el código que publicas o que piensas usar en tu app no tiene licencia, no significa que puedes hacer con él lo que quieras… sino todo lo contrario: no puedes hacer nada con él. Está ahí como en un museo solo para que la gente lo vea. Yo siendo honesto yo siempre pensé que un código sin licencia era un código libre y es por eso que si llegan a ver al día de hoy los <a href="https://github.com/fferegrino" target="_blank">repos que tengo creados</a>, casi ninguno tiene licencia, lo estaré corrigiendo en el transcurso de estos días.

Espero que ahora entiendas la importancia de poner una licencia a tu código, y no solo dejarlo ahí a la intemperie. Porque estoy seguro que si lo publicaste, no fue para que lo admiraran, y seguramente si te encuentras código así no solo quieres admirarlo, si no usarlo. Entonces, vamos a ver algunas licencias (<a href="https://www.whitesourcesoftware.com/whitesource-blog/open-source-software-licenses-trends/" target="_blank">las tres más populares</a>) y sus puntos más destacados:

## MIT  

La licencia MIT, llamada así porque fue desarrollada en el Instituto Tecnológico de Massachusetts, es una licencia bastante sencilla, y se nota en el texto. Es considerada una licencia permisiva, ya que las limitaciones para usar el software que protege son mínimas, y de hecho esta es una de las cosas que la hace más atractiva: Se puede usar con fines comerciales, se puede distribuir, modificar y mantener como código cerrado en productos derivados, siempre y cuando en estos productos derivados o modificados se informe que se hace uso del código y se añada una copia de la licencia original. En otras palabras, en tu app se debe hacer referencia a que estás usando el software, así como una copia de la licencia original.
 
Bajo esta licencia el autor original también está exento de cualquier responsabilidad que pueda darse como resultado de usar el código que creó, y sobre el cual no ofrece ninguna garantía.

Como ejemplos de proyectos que usan esta licencia está: <a href="https://github.com/xamarin/Xamarin.Forms/blob/master/LICENSE" target="_blank">Xamarin.Forms</a>, <a href="https://github.com/dotnet/core/blob/master/LICENSE.TXT" target="_blank">.NET Core</a>, <a href="https://jquery.org/license/" target="_blank">jQuery</a>, <a href="https://github.com/rails/rails/blob/master/activerecord/MIT-LICENSE" target="_blank">Rails</a>. 

## GPL

Esta es la segunda licencia más usada, y es una licencia muy controversial, ya que mientras que permite distribuir, modificar, comerciar los productos derivados, también impone más restricciones, las primeras dos no parecen ser un "problema": debes incluir una copia de la licencia (al igual que en la del MIT), y además debes indicar qué cambios le hiciste al software original. Lo controversial es que si distribuyes el software creado, debes liberar el código fuente, y que este esté publicado bajo la misma licencia, GPL, también por lo cual muchos la conocen como una licencia viral, ya que en cuanto se comience a usar por un proyecto, cualquier derivado tiene que usarla, lo cual a muchos (principalmente empresas) es algo que no les parece tan atractivo. 

Esta licencia también indica que el código no ofrece garantías y exenta al creador original de cualquier responsabilidad.

La licencia GPL entre sus proyectos el <a href="https://www.kernel.org/pub/linux/kernel/COPYING" target="_blank">kernel de Linux</a>, el programa de animación <a href="https://www.blender.org/about/license/" target="_blank">Blender</a> y el famoso <a href="http://www.videolan.org/legal.html" target="_blank">reproductor VLC</a>.

## Apache

La licencia Apache, es en muchos aspectos similar a la del MIT, en el aspecto de que es permisiva y se puede hacer con el código casi todo lo que se desee. En este caso, si usas el software bajo Apache en tu programa debes indicarlo, así como indicar qué modificaciones le hiciste al software  y agregar una copia de la licencia original. Tu proyecto puede estar licenciado bajo otra distinta a Apache. Esta librería suele resultar atractiva para empresas ya que además ofrece protecciones de marca y de patentes. Algo que las dos anteriores no tienen dentro de su alcance.

Como grandes ejemplos de proyectos que usan esta licencia está <a href="https://source.android.com/source/licenses" target="_blank">Android</a>, <a href="http://getbootstrap.com/getting-started/#license-faqs" target="_blank"><s>Bootstrap</s></a> y el lenguaje de programación <a href="https://github.com/apple/swift/blob/master/LICENSE.txt" target="_blank">Swift</a>.

Si sientes que esta licencia no se ajusta a tus necesidades puedes investigar otras, hay algunos sitios (de los cuales yo me basé para sacar esta información que puedes consultar <a href="https://choosealicense.com/" target="_blank">choosealicense.com</a> y <a href="https://tldrlegal.com/" target="_blank">tldrlegal.com</a>.

O tal vez podrías utilizar la licencia 

## Haz lo que se te de tu #$@&%*! gana  

Sí esa es una licencia, básicamente puedes inventar tu propia licencia para proteger tus creaciones usándola, y este fue el caso de algunos <a href="http://www.wtfpl.net/" target="_blank">desarrolladores decidieron</a> dejar que la gente hiciera lo que quisiera con el código bajo esta licencia. Aunque no muy recomendada, esta licencia también está aprobada por la fundación de código libre.

## Licenciando otros materiales

Las mismas licencias bajo las que se libera el código fuente no son del todo compatibles con otros elementos que forman parte de un proyecto, como la documentación o los archivos multimedia. Tal vez tengas que explorar otras alternativas para licenciar estos materiales.

Y ustedes, ¿han contribuido a algún proyecto de código abierto? ¿tienen repositorios en GitHub? ¿han tenido que lidiar con la elección de licencias? cuéntenme en los comentarios. 