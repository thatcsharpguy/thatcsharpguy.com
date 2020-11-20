---
layout: post
title: Servicios web resistentes en aplicaciones móviles
date: 2016-08-15 19:00:01
author: Antonio Feregrino
excerpt: Llevamos nuestros dispositivos con nosotros a donde sea. Los tenemos en casa, en el trabajo y durante las vacaciones. Están con nosotros sin importar si estamos conectados a internet wifi de 1 gigabit o en una red 4g.
featured_image: featured.jpg
images_folder: /xamarin-forms/resilient/
github: https://github.com/ThatCSharpGuy/Akavache-sample
tweet_id: 765351288318025728
lang: es
tags: Xamarin, XamarinForms, NuGetRecomendado
featured_tag: Xamarin
---

 > Este es post es una traducción del post de <a href="https://twitter.com/RobGibbens" target="_blank">Rob Gibbens</a> llamado <a href="http://arteksoftware.com/resilient-network-services-with-xamarin/" target="_blank">Resilient network services with mobile Xamarin apps</a>, te invito a visitar el post y el resto de su blog.  

Durante mucha de la historia de la computación, nuestras computadoras y nuestras aplicaciones estuvieron en computadoras y no se movían. Nosotros contábamos con una fuente constante de energía, recursos y acceso a la red. Los desarrolladores no necesitaban pasar mucho tiempo previendo interrupciones o fallas con esos recursos. Inclusive era común tener aplicaciones que funcionaban localmente, en las que ni siquiera teníamos que pensar en la red. 

## Vivimos en un mundo móvil  
Llevamos nuestros dispositivos con nosotros a donde sea. Los tenemos en casa, en el trabajo y durante las vacaciones. Están con nosotros sin importar si estamos conectados a internet wifi de 1 gigabit o en una red 4g. Tienen que funcionar mientras viajamos en túneles, trenes, automóviles, volando a 9000 metros de altura y cuando no tenemos conexión. Como desarrolladores debemos, no solo esperar esos requisitos, sino tomarlos en cuenta desde el desarrollo inicial y la arquitectura de nuestra aplicación móvil.  
  
## Enfoque actual  
Cuando empezamos a escribir nuestras apps con Xamarin, probablemente optamos por el enfoque sencillo de escribir el código que se conecta con la red nosotros mismos. Tal vez solamente usamos la librería *HttpClient* de Microsoft para hacer una llamada, y después *Json.net* para deserializar el resultado. O puede que nos luzcamos e incluyamos algunas otras librerías también. Puedes ver este enfoque en el post de Rob [End to End Mvvm with Xamarin](http://arteksoftware.com/end-to-end-mvvm-with-xamarin/) en donde muestra la implementación de un cliente para un servicio.  
  
```csharp  
namespace DtoToVM.Services  
{
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;
    using AutoMapper;
    using Newtonsoft.Json;
    using DtoToVM.Dtos;
    using DtoToVM.Models;

    public class TekConfClient
    {
        public async Task<List<Conference>> GetConferences ()
        {
            IEnumerable<ConferenceDto> conferenceDtos = Enumerable.Empty<ConferenceDto>();
            IEnumerable<Conference> conferences = Enumerable.Empty<Conference> ();

            using (var httpClient = CreateClient ()) {
                var response = await httpClient.GetAsync ("conferences").ConfigureAwait(false);
                if (response.IsSuccessStatusCode) {
                    var json = await response.Content.ReadAsStringAsync ().ConfigureAwait(false);
                    if (!string.IsNullOrWhiteSpace (json)) {
                        conferenceDtos = await Task.Run (() => 
                            JsonConvert.DeserializeObject<IEnumerable<ConferenceDto>>(json)
                        ).ConfigureAwait(false);

                        conferences = await Task.Run(() => 
                            Mapper.Map<IEnumerable<Conference>> (conferenceDtos)
                        ).ConfigureAwait(false);
                    }
                }
            }

            return conferences.ToList();
        }

        private const string ApiBaseAddress = "http://api.tekconf.com/v1/";
        private HttpClient CreateClient ()
        {
            var httpClient = new HttpClient 
            { 
                BaseAddress = new Uri(ApiBaseAddress)
            };

            httpClient.DefaultRequestHeaders.Accept.Clear();
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            return httpClient;
        }
    }
}
```  

Este código funciona, pero no toma en cuenta ningún error en la red. Si la red estuviera caída, el servicio no respondiera u ocurriera alguna excepción, nuestra aplicación se cerraría. Evidentemente, esto no es lo óptimo.  
  
## Objetivos  
Los objetivos de nuestras apps deben incluir, pero no limitarse, a los siguientes:  

 - Acceso sencillo a servicios *restful*
 - Rápida respuesta para nuestros usuarios  
 - Funcionar *offline*  
 - Manejo de errores

Como objetivos secundarios:  

- Desarrollo en poco tiempo  
- Facilidad de mantenimiento  
- Reusar librerías existentes  

Ataquemos estos objetivos uno a la vez, y veremos cómo podemos mejorar nuestra aplicación conectada. Como casi siempre, estaré usando una la app de una conferencia basada en TekConf.

> El código de ejemplo está disponible en [el GitHub de Rob](https://github.com/RobGibbens/ResilientServices)  

## Acceso fácil a servicios *restful*  

### Refit  

```  
PM> Install-Package Refit
```  

La primera cosa que vamos a necesitar es una forma para acceder a nuestros servicios. **Podríamos** usar HttpClient + Json.net como hicimos en el ejemplo anterior, sin embargo, podemos hacer esto más simple. De nuevo, uno de nuestros objetivos secundarios es reusar librerías existentes. La primera que usaremos es <a href="https://github.com/paulcbetts/refit" target="_blank" rel="nofollow">Refit</a>. Refit nos permite definir una interfaz que describe la API que estamos llamando, y el framework se encarga de hacer la llamada al servicio y deserializar el objeto de regreso.  

En nuestro caso, la interfaz se verá más así:  

```csharp  
[Headers("Accept: application/json")]
public interface ITekConfApi  
{
    [Get("/conferences")]
    Task<List<ConferenceDto>> GetConferences();

    [Get("/conferences/{slug}")]
    Task<ConferenceDto> GetConference(string slug);
}
```  

Aquí estamos declarando que nuestra API retornará *json*, y que hay dos "métodos" (recursos) que podemos llamar. El primer método es una petición HTTP GET al *endpoint* `/conferencias`. El segundo método es también una petición HTTP GET, y pasa un argumento como parte de la url para obtener una sola conferencia.  

Una vez que la interfaz ha sido definida, usarla es tan simple como esta:  

```csharp  
var tekconfApi = RestService.For<ITekConfApi>("http://api.tekconf.com/v1");

var conferences = await tekconfApi.GetConferences();

var codemash = await tekconfApi.GetConference("codemash-2016");  
```  

## Fast response for our users  

### Akavache  

```  
PM> Install-Package Akavache
```  

Ahora que tenemos una forma sencilla de acceder al servicio, nos podemos concentrar en la experiencia de usuario. El desempeño de una aplicación móvil, desde la perspectiva del usuario, es **crítica**. No importa en realidad si tu aplicación **ES** rápida mientras que el usuario **PIENSE** que lo es.  
  
La mejor manera de acelerar una llamada a un servicio es no hacer esa llamada en primer lugar. Cargar la información almacenada localmente es exponencialmente más rápido que hacer una petición web, especialmente cuando estamos en un dispositivo móvil conectado en una red celular lenta. Aquí, podemos usar la técnica común de cachear los datos. Cuando la página termine de cargar y solicite datos para mostrar, nosotros inmediatamente queremos la información cacheada en nuestro dispositivo y mostrarla. Desde la perspectiva del usuario, la página se muestra inmediatamente. Mientras esto ocurre, nosotros consultamos al servicio remoto, obtenemos la nueva información y la almacenamos en cache. Dado que el usuario no necesita esperar a que la consulta al servicio concluya, podemos ejecutarla a nuestra discreción y conseguirnos un poco más de tiempo para procesarla.  

Mientras que pudimos haber escrito toda la lógica de cacheo nosotros mismos, en su lugar agregamos un paquete de NuGet llamado <a href="https://github.com/akavache/Akavache" target="_blank" rel="nofollow">Akavache</a>. Del sitio de Akavache:  

 > Akavache es un repositorio de *clave-valor* asíncrono y persistente (i. e. escribe a disco) creado para escribir aplicaciones de escritorio y móviles en C#, basado en SQLite3. Akavache es ideal para almacenar tanto información importante (i. e. configuración de usuarios) así como información local que debe expirar.  

```csharp  
public async Task<List<ConferenceDto>> GetConferences()  
{
    var cache = BlobCache.LocalMachine;
    var cachedConferences = cache.GetAndFetchLatest("conferences", GetRemoteConferencesAsync,
        offset =>
        {
            TimeSpan elapsed = DateTimeOffset.Now - offset;
            return elapsed > new TimeSpan(hours: 0, minutes: 30, seconds: 0);
        });

    var conferences = await cachedConferences.FirstOrDefaultAsync();
    return conferences;
}
```  

Podemos usar el método `GetAndFetchLatest` de Akavache para devolver inmediatamente las conferencias almacenadas, si hay alguna. Al mismo tiempo, preparamos una petición a nuestro método `GetRemoteConferencesAsync`, quién hará la llamada al servicio remoto si el periodo de tiempo indicado ha transcurrido.  

Matthew Soucop tiene un muy buen <a href="http://codemilltech.com/akavache-is-aka-awesome/" target="_blank" rel="nofollow">post de Akavache</a>. 

### ModernHttpClient  

```  
PM> Install-Package ModernHttpClient
```  

A pesar de que nos gustaría siempre obtener los datos de la cache, sabemos que en algún momento tendremos que llamar al servicio remoto. Sin embargo, en la *Xamarin stack*, nos encontramos con un problema, por default Mono (y por tanto Xamarin) usa la *Mono networking stack*. Funciona, pero Apple y Google hay pasado un montón de tiempo optimizando su *stack* de redes en sus respectivas plataformas, y cando usamos `HttpClient` estamos pasando par algo dichas optimizaciones por completo. Podemos arreglar esto añadiendo <a href="https://github.com/paulcbetts/ModernHttpClient" target="_blank" rel="nofollow">ModernHttpClient</a>.  
  
 > Esta librería lleva las librerías de red más recientes a Xamarin a través de un tipo `HttpClient` modificado. Escribe tu aplicación usando `System.Net.Http` pero coloca esta librería e irá drásticamente más rápido.  

```csharp  
var client = new HttpClient(new NativeMessageHandler())  
{
    BaseAddress = new Uri(apiBaseAddress)
};

return RestService.For<ITekConfApi>(client);
```  

Al pasar `NativeMessageHandler` al constructir de `HttpClient`, automáticamente estamos usando el *stack* apropiado en cada plataforma.  
  
### Fusillade  

```  
PM> Install-Package Fusillade
```  

Desde la perspectiva del usuario, no todas las peticiones al servicio son iguales. Peticiones que son iniciadas por una acción del usuario deben tener una prioridad que cualquier otra que la app haga por su cuenta. Recuerda que nuestro objetivo es que el usuario **sienta** que la app responde rápido.  
  
<a href="https://github.com/paulcbetts/Fusillade" target="_blank" rel="nofollow">Fusillade</a> es otro paquete de NuGetque vamos a usar para contar con:  

 - Evitar duplicar peticiones automáticas
 - Limitar peticiones
 - Priorizar peticiones 
 - Contar con peticiones especulativas  

```csharp  
public class ApiService : IApiService  
{
    public const string ApiBaseAddress = "http://api.tekconf.com/v1";

    public ApiService(string apiBaseAddress = null)
    {
        Func<HttpMessageHandler, ITekConfApi> createClient = messageHandler =>
        {
            var client = new HttpClient(messageHandler)
            {
                BaseAddress = new Uri(apiBaseAddress ?? ApiBaseAddress)
            };

            return RestService.For<ITekConfApi>(client);
        };

        _background = new Lazy<ITekConfApi>(() => createClient(
            new RateLimitedHttpMessageHandler(new NativeMessageHandler(), Priority.Background)));

        _userInitiated = new Lazy<ITekConfApi>(() => createClient(
            new RateLimitedHttpMessageHandler(new NativeMessageHandler(), Priority.UserInitiated)));

        _speculative = new Lazy<ITekConfApi>(() => createClient(
            new RateLimitedHttpMessageHandler(new NativeMessageHandler(), Priority.Speculative)));
    }

    private readonly Lazy<ITekConfApi> _background;
    private readonly Lazy<ITekConfApi> _userInitiated;
    private readonly Lazy<ITekConfApi> _speculative;

    public ITekConfApi Background
    {
        get { return _background.Value; }
    }

    public ITekConfApi UserInitiated
    {
        get { return _userInitiated.Value; }
    }

    public ITekConfApi Speculative
    {
        get { return _speculative.Value; }
    }
}
```  

Ahora, en lugar de solamente usar `HttpClient`, tenemos una clase `ApiService` que tendrá tres instancias de `Refit`, para peticiones iniciadas por el usuario (*UserInitiated*), en segundo plano (*Background*) y especulativas (*Speculative*).  
  
Cuando la página carga por primera vez, nosotros automáticamente trataremos de obtener información sobre las conferencias. Dado que el usuario no inició esta petición, podemos enviar esta petición a segundo plano.  

```csharp  
var conferences = await _conferencesService  
                        .GetConferences(Priority.Background)
                        .ConfigureAwait(false);
```  

Si el usuario selecciona el botón de actualizar, entonces tendríamos que correr esta petición con diferente prioridad.  

```csharp  
var conferences = await _conferencesService  
                        .GetConferences(Priority.UserInitiated)
                        .ConfigureAwait(false);
```  

Cuando las conferencias *regresan* del servicio, podríamos asumir que el usuario seleccionará una de ellas para ver sus detalles. Puesto que solo estamos especulando que esto podría ocurrir, podemos preparar una petición para obtener los detalles de las conferencias con prioridad especulativa:  

```csharp  
foreach (var slug in conferences.Select(x => x.Slug))  
{
    _conferencesService.GetConference(Priority.Speculative, slug);
}
```  

> Una nota de Paul Betts: "Si usas la prioridad especulativa, tienes que llamar a ResetLimit en tu app móvil para reestablecer el límite de descarga de 5MB cada que navegues entre páginas (esta no es una regla estricta, pero es una buena idea - básicamente cuando sepas que el usuario está "iniciando una nueva sesión"). La prioridad especulativa es definitivamente algo que la mayoría de apps no necesitan - imagínate una app de Reddit en donde intentes descargar cada elemento en la página, pero en realidad no quieres hacer eso, tal vez solo intentes adivinar qué es lo que el usuario quiere ver. Entonces el desarrollador puede ser flojo y decir "Descarga todo, *yolo*" y Fusillade las eliminará basándose en el contenido de cada una.  
  
## Funcionamiento *offline*  

Contrario a las aplicaciones de escritorio, nuestras aplicaciones móviles deben ser alguna funcionalidad mientras están desconectadas de la red. La peor cosa que podríamos hacer es que falle cuando tratamos de hacer una petición. Lo mejor que podríamos hacer es que siga funcionando para que el usuario no se de cuenta de que no estaba conectado a internet.  

### Connectivity  

```  
PM> Install-Package Xam.Plugin.Connectivity
```  

Si queremos estar seguros de que no causaremos un excepción al hacer una petición cuando no hay internet, entonces necesitamos una manera de conocer el estado de la conexión. Cada plataforma tiene su manera de hacer esta validación, pero nosotros queremos hacer uso de ella de una manera *cross-platform* desde nuestra PCL.  
  
<a href="https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Connectivity" target="_blank" rel="nofollow">Connectivity</a> es un plugin para Xamarin que nos permite hacer justamente eso.  
  
> Simple plugin multiplataforma para revisar el estado de la conexión en un dispositivo móvil, obtener el tipo de conexión, el ancho de banda y más.  
  
Antes de hacer una petición de red, podemos revisar si el dispositivo está conectado.  
  
```csharp  
if (CrossConnectivity.Current.IsConnected)  
{
    conferences = await _apiService.Background.GetConferences();
}
return conferences;
```  

### Akavache  
Ya vimos cómo Akavache nos permite continuar trabajando mientras estamos offline al cachear los resultados de consultas localmente. Al combinar Akavache y las llamadas especulativas de Fusillade, podemos cachear proactivamente tantos datos como podemos mientras permanece conectado. Cuando no haya red, la app continuará funcionando en modo de solo lectura.  
  
## Manejo de errores  
En un mundo perfecto, nuestro código funcionaría correctamente todo el tiempo. Este no es un mundo perfecto. Las redes se caen. Los servicios lanzan errores. El código falla. Algunos de los errores son permanentes, pero un gran número son errores esporádicos. Las peticiones por red son poco confiables y las APIs tienen errores esporádicos por una amplia gama de razones.  

## Polly  
 
```  
PM> Install-Package Polly
```  

<a href="https://github.com/michael-wolfenden/Polly" target="_blank" rel="nofollow">Polly</a> es una de las librerías más útiles que he usado en un tiempo. De su sitio web:  

> Polly es una librería para .NET 3.5/4.0/4.5/PCL que permite a los desarrolladores expresar políticas de manejo de errores como Reintentar (*Retry*), Reintentar para siempre (*Retry Forever*), espera y reintenta (*Wait and Retry*) o corta circuito (*Circuit Breaker*) de una manera fluida.  

Polly nos permite manejar fácilmente este tipo de errores de manera consistente y coherente. En este ejemplo, trataremos de conectarnos al servicio 5 veces, con una espera que incrementa exponencialmente 2, 4, 8, 16, 32 segundos entre intentos. Esto debería darle tiempo al dispositivo de reestablecer la conexión y continuar con las peticiones.  

```csharp  
conferences = await Policy  
      .Handle<WebException>()
      .WaitAndRetry
      (
        retryCount:5, 
        sleepDurationProvider: retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt))
      )
      .ExecuteAsync(async () => await getConferencesTask);
```  

### AsyncErrorHandler  
 
```  
PM> Install-Package AsyncErrorHandler.Fody  
```  

Inclusive con todo el cacheo, los reintentos y planeación que hemos puesto en el código, en algún momento fallará. Queremos que cuando eso suceda, lo manejemos de buena manera.  
  
En nuestras aplicaciones móviles, es imperativo que usemos `async`/`await` tanto como podamos para asegurarnos de no bloquear el hilo de la UI mientras hacemos cosas como peticiones de red. Manejar las excepciones en métodos asíncronos puede ser complicado.  
  
Agregar <a href="https://github.com/Fody/AsyncErrorHandler" target="_blank" rel="nofollow">AsyncErrorHandler</a> nos permite manejar esas excepciones de modo global, así nos aseguramos de que no terminarán nuestra app.  

## Más  
Podríamos ir un paso más allá en la arquitectura de nuestro código para manejar las peticiones de red. Podríamos registrar cada llamada como una <a href="https://developer.xamarin.com/guides/ios/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/ios_backgrounding_with_tasks/" target="_blank" rel="nofollow">BackgroundTask</a> en iOS, o como un <a href="http://developer.xamarin.com/guides/android/application_fundamentals/services/" target="_blank" rel="nofollow">Service</a> en Android para darle a cada una la oportunidad de terminar aún cuando la app es enviada a segundo plano.  

Podríamos implementar una cola, o laguna forma de sincronización de datos que nos permitirían actualizar los datos mientras estamos offline y sincronizarlos con el servidor cuando una conexión esté disponible. Qué tan lejos quieres llegar, depende de ti.  

Fundamentalmente, el desarrollo móvil introduce algunas complejidades por las que no habíamos tenido que preocuparnos en el desarrollo para escritorio. Una aplicación móvil que no usa servicios externos es una isla con usabilidad limitada. Una aplicación móvil que usa servicios externos, pero falla cuando trata de acceder a ellos es inútil. Al usar algunas librerías realmente geniales, podemos asegurarnos de que nuestras aplicaciones darán a nuestros usuarios la mejor experiencia.  
 
## Agradecimientos  
Para hacer que todo esto funcione, aproveché el gran trabajo de otros desarrolladores. *Sentado sobre los hombros de gigantes*.  

Gracias a <a href="https://twitter.com/jamesmontemagno" target="_blank" rel="nofollow">James Montemagno</a> (<a href="http://motzcod.es/" target="_blank">Blog</a>, <a href="https://github.com/jamesmontemagno" target="_blank">GitHub</a>) por el plugin <a href="https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Connectivity" target="_blank">Connectivity</a>.

Gracias a Michael Wolfenden (<a href="https://github.com/michael-wolfenden/" target="_blank">GitHub</a>) por el fantástico framework <a href="https://github.com/michael-wolfenden/Polly" target="_blank">Polly</a>.  

Gracias a <a href="https://twitter.com/SimonCropp" target="_blank">Simon Cropp</a> (<a href="https://github.com/SimonCropp" target="_blank">GitHub</a>) por <a href="https://github.com/Fody/" target="_blank">Fody</a> y <a href="https://github.com/Fody/AsyncErrorHandler" target="_blank">AsyncErrorHandler</a>.  

Gracias a <a href="https://twitter.com/GeoffreyHuntley" target="_blank">Geoffrey Huntley</a> (<a href="https://www.ghuntley.com/" target="_blank">Blog</a>, <a href="https://github.com/ghuntley" target="_blank">GitHub</a>) por el código y la inspiración para la clase <a href="https://gist.github.com/ghuntley/ed2eb754a47e2cd3bc90" target="_blank">ApiService con Fusillade y Refit</a>. 

Muchas, muchas gracias a <a href="https://twitter.com/paulcbetts" target="_blank">Paul Betts</a> (<a href="http://log.paulbetts.org/" target="_blank">Blog</a>, <a href="https://github.com/paulcbetts" target="_blank">GitHub</a>) por sus trenemdas contribuciones a la comunidad *open source* de Xamarin, incluyendo <a href="https://github.com/paulcbetts/refit" target="_blank">Refit</a>, <a href="https://github.com/akavache/Akavache" target="_blank">Akavache</a>, <a href="https://github.com/paulcbetts/Fusillade" target="_blank">Fusillade</a>, y <a href="https://github.com/paulcbetts/ModernHttpClient" target="_blank">ModernHttpClient</a>.  
  
## Código fuente  
Puedes encontrar un ejemplo completo en el <a href="https://github.com/RobGibbens/ResilientServices" target="_blank">GitHub de Rob</a>.