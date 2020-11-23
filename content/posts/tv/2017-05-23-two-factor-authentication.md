---
title:  Autenticación de dos factores
date:  2017-05-23 18:00:00
youtube:  j0BEPJygv4E
images_folder:  /tv/2fa/
summary:  La autenticación de dos factores da más seguridad a los usuarios al establecer una segunda barrera de autenticación en sus cuentas.
featured_image:  featured.jpg
tags:  Meta, Tv
featured_tag:  Tv
template:  video
---

La autenticación multifactor es un método de control de acceso  que se basa en que el usuario que quiere acceder a un sistema presente más de una prueba para comprobar su identidad. Este puede ser usado tanto para lugares físicos como un edificio de oficinas, como en lugares virtuales, por ejemplo, una cuenta de correo.

Los factores que se usan para corroborar la identidad pertenecen a alguna de estas categorías:  

 - De conocimiento, o algo que los usuarios saben. Como la contraseña de tu correo, o la respuesta a una pregunta. Como cuando de niño hacías un club y solo podían entrar quienes se supieran la frase.
 - De posesión, o algo que los usuarios tienen. Como cuando en la escuela o en el trabajo te dan una credencial que te identifica como perteneciente a esa organización. 
 - De inherencia, o algo que los usuarios son. Como cuando en las películas de James Bond ves que tienen lectores de iris y de huellas dactilares, o, mejor dicho, información biométrica que es única para cada individuo.  
 - De ubicación, o el lugar en el que el usuario está.  
 - De tiempo, o el momento en el que el usuario intenta a acceder.

En particular los tres primeros: de conocimiento, de posesión y de inherencia son los más usados en general, mientras que los de ubicación y tiempo tienen usos particulares.

Anteriormente era muy común emplear solo el factor de conocimiento para conceder el acceso a un usuario, e inclusive estoy seguro que muchos de los que están viendo este video aún siguen usando uno solo, espero que después de ver este video eso cambie.

## Por qué  

Pero, ¿por qué existe esta forma de autenticación?  

El objetivo de la autenticación de múltiples factores es dificultar  el acceso a usuarios no autorizados a un sistema al establecer varios métodos de validación de identidad. 

¿Qué pasaría si alguien se enterara de tu contraseña de tu correo? esa persona ya podría comprobar que "eres tú" con el factor de conocimiento. Pero si tuvieras activada la autenticación de dos factores con una app en tu celular, a esa persona no le bastaría conocer tu contraseña, sino que además tendría que tener acceso a tu celular para comprobar que eres tú. Lo cual hace aún más difícil que pueda acceder.


## Métodos comunes 

Es poco común encontrar autenticación de más de dos factores en aplicaciones de uso común, como el correo, banca en línea o tu cuenta de Facebook, lo normal es que se usen únicamente dos: Estos métodos de autenticación es el de conocimiento y posesión.

Muchas compañías actualmente ofrecen el método de posesión a través de mensajes SMS o llamadas a un número, sin embargo, este método ya no es considerado tan seguro debido a que alguien podría clonar tu tarjeta SIM, obtenerla directamente de tu proveedor o simplemente robar tu teléfono.

Compañías como Google, GitHub ofrecen la posibilidad de obtener una app generadora de códigos que previamente registramos para obtener un código y poder entrar.

Las ventajas de usar un teléfono móvil como una forma de comprobar que un usuario es quien dice ser radica en normalmente el usuario ya tiene el dispositivo y no hay necesidad de otorgar un dispositivo extra al usuario. Tal y como lo hacen los bancos con estos tokens: que también son otro método de demostrar la posesión. 

## Yubikey  

Como usuarios también podemos obtener otros dispositivos para autenticarnos como este tipo de llaves USB, esta en particular se llama Yubikey pero puedes conseguir de otra marca. El uso es bastante sencillo, por ejemplo, una vez registrada con Facebook, puedes usarla para entrar a tu cuenta. Pones tu password y después tienes que introducir la llave en tu computadora, presionar el botón y listo. Estás autenticado.

Hay una lista bastante grande de que compañías que <a href="https://www.yubico.com/support/partners/" target="_blank">ofrecen soporte</a> para las Yubikey.

## Pero, ¿a mi qué demonios me importa?
Espero que no te estés preguntando para qué deberías saber tu esto, ya  que usando tu mismo la autenticación de dos factores estarías protegiendo tu información así como la de la empresa en la que trabajas.  

Pero la cosa es mejor, como desarrollador tu puedes implementar este método de control de acceso, otorgándole así más opciones de seguridad a tus usuarios. Podrías comenzar por hacer compatibles tus logins con apps que generan códigos de un solo uso como Google Authenticator. <a href="..\two-factor-authentication-asp-net" target="_blank"><strong>Si quieres ver un ejemplo usando ASP.NET Core, da click aquí</strong></a>. Pero si te sientes realmente inspirado podrías visitar el sitio de Yubico para ver cómo es que puedes comenzar a usar estas llavecitas para que tus usuarios se *logueen* en tus apps.

Espero les haya gustado y que les haya motivado también a mejorar la seguridad en sus cuentas. Y si así fue les pido que le den Me Gusta, lo compartan y se suscriban al canal si es que aún no lo han hecho.  

Nos vemos la próxima.  