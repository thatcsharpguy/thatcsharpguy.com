---
title:  Las pruebas unitarias
date:  2017-03-14 18:00:00
youtube:  8rKy1juvVmI
images_folder:  /tv/unit-test/
summary:  Estas pruebas es donde componentes individuales del software se someten a pruebas. El propósito de estas es asegurar que cada unidad de trabajo funciona individualmente como debería. Es decir, funciona cuando se supone que debe funcionar, responde con lo que se supone que tiene que responder, falla como y cuando se supone que debe fallar.
featured_image:  featured-yt.jpg
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

Más seguido de lo que pensamos, nosotros, los desarrolladores llegamos a pensar que nuestro código carece de errores. Todo está bien, después de todo nosotros lo escribimos, probamos dos o tres casos y nunca falló y según nosotros está listo para ser publicado en producción... pero, ¿qué pasa cuando alguien nos pregunta "Y si probaste el código"? uno no puede decir que sí sin sentirse nervioso.  

Esto sí pasa, yo lo he hecho y no estoy orgulloso de eso. Es por eso que a modo de reparación trataré de explicar de qué se tratan las pruebas de código... y más en específico: las pruebas unitarias.  

Hay 4 tipos de pruebas generalmente usadas en el desarrollo del software, comenzando desde el más básico hasta el más complejo: las pruebas unitarias, integración, de sistema y de aceptación. De cierto modo, una depende de la otra.  

## Pruebas unitarias

Estas pruebas son un en donde componentes individuales del software se someten a pruebas. El propósito de estas es asegurar que cada unidad de trabajo funciona individualmente como debería: es decir, funciona cuando se supone que debe funcionar, responde con lo que se supone que tiene que responder, falla como y cuando se supone que debe fallar. Se supone que los tests unitarios deben probar la unidad mínima de trabajo de un programa que es aquella que devuelve un valor o produce un cambio en el estado del programa, generalmente hablaríamos de un solo método o una función. Aunque no siempre es así.   

### Estructura    

Para generar una prueba untaria es necesario contar con dos cosas: un conjunto de valores de entrada y un conjunto de valores esperados como resultado de la ejecución de las pruebas.  

Una prueba unitaria tiene una estructura particular, se divide en tres secciones:  

 - Preparación: en donde se establecen las condiciones iniciales con las que se va a ejecutar la prueba, así como el resultado esperado.  
 - Acción: en donde se ejecuta el componente de software que se está probando.
 - Comprobación: En donde se ha obtenido el resultado de la *Acción* y se comprueba que el resultado de esta coincida con el resultado esperado.  


### Responsable de ejecutar las pruebas  
Como desarrollador tu debes ser el principal responsable de probar tu código a nivel más básico a través de pruebas unitarias, sin embargo debes de tener cuidado y ser muy autocrítico. Y es que sin quererlo podrías estar escribiendo pruebas que no sean tan útiles ya que al tener conocimiento sobre el comportamiento interno de tu código, no escribes pruebas que lo lleven al límite.

### Casos a probar  
En realidad es muy probable que para el código que escribas exista una infinidad de posibilidades de entrada. Sin embargo existen algunos casos que debes considerar como los casos más básicos y esperados en el funcionamiento normal del programa, también casos *al limite* que si bien pueden suceder no es tan probable que existan (si tu función recibe enteros de 32 bits, prueba con el valor máximo de un entero). Casos con valores de entrada inválidos o no esperados, así como también se vale agregar un poco de intuición: si sientes que bajo determinadas condiciones tu código puede fallar, escribe un test con dichas condiciones.

Así mismo, si una vez que tu piensas que se has terminado el desarrollo y más adelante se descubre un bug en el código, escribe una prueba unitara que lo reproduzca antes de intentar resolverlo.  

### Otras características  
Para que los test sean efectivos y útiles deben cumplir con otras caracterísitcas, como:  

- Independientes del entorno: deben funcionar en tu computadora, en la de tu compañero, en el servidor de producción… en donde sea.  
- No dependen de otros tests: Cada test debe ser independiente de los otros. Que uno se ejecute como debería no debe depender de la ejecución de otro.  
- No dependen en fuentes externas de datos: el test debe ser autocontenido, si se requiere recuperar información de fuentes externas (como una base de datos o una API web) se deben "pretender" que esta información ya existe mediante una técnica llamada *mocking*, de la que puedes saber más aquí: <a href="https://es.wikipedia.org/wiki/Objeto_simulado" target="_blank">Objeto simulado (Wikipedia)</a> y <a href="http://www.michaelminella.com/testing/the-concept-of-mocking.html" target="_blank">The concept of mocking</a>.    
- Son automatizables: Este es uno de los puntos medulares de las pruebas unitarias. No deben depender de interacción humana y deben poder ejecutarse automáticamente sin supervisión.  
- Se ejecutan rápido: acá hay que tener cuidado, el hecho de que un test sea rápido significa que la cantidad de tiempo que se toma en la etapa de preparación y de comprobación es mínima. La ejecución tomará lo que tenga que tomar.  
- Son mantenibles: Las pruebas unitarias no dejan de ser código, y como tal debes tratarlas: siguendo convenciones de nombres, saltos de línea… 

<blockquote>Aprende a crear pruebas unitarias usando Visual Studio, <a href="..\pruebas-unitarias" target="_blank">ve mi otro vídeo</a></blockquote>

## Pruebas de integración  
Las pruebas de integración se basan en las pruebas unitarias, y es que mientras que las anteriores prueban elementos individuales, las pruebas de integración se crean con la finalidad de verificar que estos elementos interactuen correctamente entre ellos.  

## Prueba del sistema  
Una vez que se ha corroborado la integración entre los componentes, se ejecutan pruebas del sistema por completo. Usualmente se ejecutan flujos de trabajo completos.  

## Pruebas de aceptación  
Estas pruebas son similares a las pruebas del sistema, solo que a este nivel las pruebas son orientadas a verificar que el producto desarrollado cumple con los estándares y requerimientos establecidos por el cliente y el encargado de desarrollar el software.  

## Otros tipos de pruebas  
A partir de estas pruebas se genera todo un ecosistema compuesto por pruebas que cubren ciertos aspectos del desarrollo como las pruebas de regresión, de caja negra o caja blanca, de usabilidad... en fin, hasta hay una filosofía de desarrollo que está completamente basada en la creación y ejecución de pruebas.  
