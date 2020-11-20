layout: post
title: OAuth en Forms con Xamarin.Auth
date: 2016-10-17 19:00:01
author: Antonio Feregrino
excerpt: Con Xamarin.Auth, el incorporar servicios de terceros como Twitter y Facebook nunca había sido tan fácil, en este post, aprende a hacerlo desde Xamarin.Forms.
featured_image: featured.jpg
images_folder: /xamarin/auth/
github: https://github.com/fferegrino/Authy
lang: es
tags: Xamarin, XamarinForms, NuGetRecomendado
featured_tag: XamarinForms

<div class="warning">
Este post debe ser usado como guía, como sabrás, en ningún momento es buena idea dejar poner en tu código cualquier tipo de llave que no quieres que sea pública (como los AppSecrets o los Tokens). Te recomiendo visitar estos elnaces: <a href="http://www.androidauthority.com/how-to-hide-your-api-key-in-android-600583/" target="blank">How to hide your key</a> y <a href="https://stormpath.com/blog/the-ultimate-guide-to-mobile-api-security" target="blank">The Ultimate Guide to Mobile API Security</a> para mayor información.
</div>
Es un hecho que las redes sociales están dominando el mundo, y que tu app tenga conexión con alguna (o varias) de ellas es una característica que tal vez tus usuarios apreciarían en ella. Sin embargo, para acceder a ellas es necesario contar con el permiso de los usuarios, ¿cierto?  

Para nuestra suerte, muchos servicios proporcionan una forma rápida y estandarizada de solicitar dicho permiso de ellos y es través de <a href="https://oauth.net" target="_blank">OAuth</a>. Sin embargo, el proceso que ocurre despues de que los usuarios nos han dado permiso no es <a href="https://oauth.net/core/diagram.png" target="_blank">tan sencillo como parece</a>, para nuestra suerte, **Xamarin.Auth** nos ofrece una forma de ejecutar este proceso de manera sencilla y en unas cuantas líneas de código.  

En este post, trataré de explicar cómo usar Xamarin.Auth para conectarse con GitHub, Facebook y Twitter.

## Registrando aplicaciones en servicios  
Primero que nada, tenemos que registrar nuestras aplicaciones en los servicios de terceros: 

### GitHub  
Lo primero que tienes que hacer es darte de alta en el programa de desarrolladores de GitHub en <a href="https://developer.github.com/program/" target="_blank">este enlace</a> y una vez que lo hayas hecho, debes dirigirte a <a href="https://github.com/settings/developers" target="_blank">este otro enlace</a> en donde podrás registrar tu nueva app, en la pantalla de registro debes poner los datos de tu app, pero debes colocar `https://github.com` en el campo *"Authorization callback URL"*: 

<img src="/images/xamarin__auth__github-developer-2.jpg" title=""Registro de nueva aplicación"" />

Una vez que has completado estos pasos, tu aplicación estará registrada y lista para usarse, una vez creada te aparecerán dos valores que son muy importantes y que debes guardar con mucho cuidado: 

<img src="/images/xamarin__auth__github-keys.jpg" title=""GitHub keys"" />

### Facebook  
Para Facebook la cosa no es tan diferente, debes acceder a <a href="https://developers.facebook.com" target="_blank">https://developers.facebook.com</a> y registrarte como desarrollador para después registrar tu aplicación en el mismo sitio. Ahora, después de elegir un nombre para nuestra aplicación, de proporcionar un correo electrónico para contacto y resolver un captcha, nos aparecerá una pantalla con un menú como el siguiente y en el cual debemos dar click en *"Add product"* y seleccionar *"Facebook Login"*:  

<img src="/images/xamarin__auth__fb-app-addlogin.jpg" title=""Add product"" />

Una vez que hayamos hecho esto, del lado izquierdo, aparecerá un nuevo ítem en el menú llamado, justamente, *"Facebook Login"* con dos opciones debajo, damos click en *"Settings"*, después de esto aparecerá un formulario, en donde lo que tenemos que modificar es el campo *"Valid OAuth redirect URIs"*, en donde debes colocar lo siguiente: `https://www.facebook.com/connect/login_success.html`:

<img src="/images/xamarin__auth__oauthredirectaddr.png" title=""Modificar url de redirección"" />

Guardamos los cambios y ahora, en el menú de la izquierda damos click en *"Dashboard"*, que nos llevará a la pantalla inicial de nuestra app, de donde debemos copiar el valor de *"App ID"* y listo, hemos terminado de registrar nuestra app en Facebook.

### Twitter  
Básicamente, ahora que has registrado tu app en GitHub y Facebook, sabrás más o menos qué esperar con Twitter. Primero, tienes que iniciar el registro de tu app en <a href="https://apps.twitter.com" target="_blank">https://apps.twitter.com</a>, de nuevo, debemos ingresar detalles sobre la app y, **muy importante**, en el campo *"Callback URL"* debemos colocar `https://mobile.twitter.com/home`. Una vez dada de alta, tendremos acceso a una *"Consumer Key"* y a un *"Consumer Secret"*, este último debe permanecer **siempre secreto**:  

<img src="/images/xamarin__auth__twttr-keys.jpg" title=""Llaves Twitter"" />

Bien, hemos terminado el papeleo, ahora, a escribir código:  

## En el proyecto de Forms  

Comenzaremos por crear una interfaz que especifique algunas de las propiedades comunes a la hora de realizar la autenticación:

```csharp  
public interface IKeys
{
    string ClientId { get; }
    string ClientSecret { get; }
    string Scope { get; }
    string AuthorizeUrl { get; }
    string RedirectUrl { get; }
    string RequestTokenUrl { get; }
    string AccessTokenUrl { get; }
    string ConsumerSecret { get; }
    string ConsumerKey { get; }
    string CallbackUrl { get; }
}
```  

Ahora es cuando tenemos que usar los valores obtenidos en la fase de "El Papeleo", creamos una clase por cada servicio conectado, fijate cómo es que las clases implementan la interfaz IKey:

```csharp  
public class FacebookKeys : IKeys
{
    public string ClientId { get; } = "XXXXXXXXX"; // El valor de App ID en el Dashobard
    public string AuthorizeUrl { get; } = "https://m.facebook.com/dialog/oauth/";
    public string RedirectUrl { get; } = "https://www.facebook.com/connect/login_success.html";
    // ...

public class GitHubKeys : IKeys
{
    public string ClientId => "XXXXXXXXXX"; // El valor de Client Id de GitHub
    public string ClientSecret => "XXXXXXXXXXXXXXXX"; // El valor de Client Secret de GitHub
    public string AccessTokenUrl { get; } = "https://github.com/login/oauth/access_token";
    public string AuthorizeUrl { get; } = "https://github.com/login/oauth/authorize";
    public string RedirectUrl { get; } = "https://github.com";
    // ...

public class TwitterKeys : IKeys
{
    public string ConsumerKey { get; } = "EnPK4XXXXXXXtrwXXXXXXXEm"; // Consumer Key de Twitter
    public string ConsumerSecret => "FhSHfXXXXXXXXyu0ZqXXXXXXXXXXXXhbFcr8ETG2p"; // Consumer Secret de Twitter
    public string AccessTokenUrl { get; } = "https://api.twitter.com/oauth/access_token";
    public string AuthorizeUrl { get; } = "https://api.twitter.com/oauth/authorize";
    public string RedirectUrl { get; } = "https://mobile.twitter.com/home";
    public string RequestTokenUrl => "https://api.twitter.com/oauth/request_token";
    public string CallbackUrl => "https://mobile.twitter.com/home";
    //
```  

Además de un <a href="../c-sharp-enums" target="_blank">tipo <code>enum</code></a> que represente a los servicios que podemos usar, usamos un `enum` para evitar usar cadenas mágicas en el código: 

```csharp  
public enum Services
{
    GitHub,
    Facebook,
    Twitter
}
```  

Dado que Xamarin.Auth hace uso de APIs nativas de cada platforma, sus métodos son únicamente accesibles en los proyectos "cliente" y no desde el núcleo de nuestra aplicación, es por eso que para interactuar con ella indepentiéntemente de la plataforma, necesitamos hacer uso de una interfaz: 

```csharp  
public interface IAccountManagerService
{
    List<Services> Accounts { get; }

    string GetPropertyFromAccount(Services service, string property);

    void EraseAll();
}
```  

La pantalla de autorización es una nueva `Activity` o un nuevo `UIViewController`, dependiendo de la plataforma, y como desde Forms no tenemos acceso a estas clases, de nuevo creamos una solución independiente de la plataforma. En este caso lo haremos a través de un <a href="https://developer.xamarin.com/guides/xamarin-forms/custom-renderer/contentpage/" target="_blank"><i>Custom renderer</i></a> de una `ContentPage`:

```csharp  
public class AuthorizePage : ContentPage
{
    public Services Service { get; private set; }

    public AuthorizePage(Services service)
    {
        Service = service;
    }
}
```  

Como puedes ver, tiene una propiedad llamada `Service` del tipo `Service`, a través de la cual especificaremos a cuál de los servicios nos vamos a conectar. Más adelante demostraré el uso de este tipo de páginas.

## En Xamarin.iOS y Xamarin.Android
Una vez terminado el código multiplataforma, vamos a trabajar con los proyectos de iOS y Android; como las diferencias entre un código y otro son mínimas, podemos utilizar un proyecto compartido y usar <a href="../directivas-preprocesador-c-sharp" target="_blank">directivas de preprocesador</a> para compilar determinadas porciones de código de acuerdo al proyecto que estamos ejecutando.

Comenzaremos por implementar la interfaz `IAccountManagerService` a través de la que interactuaremos con el servicio de gestión de cuentas de Xamarin.Auth:

```csharp  
using System;
using System.Linq;
using System.Collections.Generic;
using Authy.AccountManagement;

#if __IOS__
namespace Authy.iOS.AccountManagement
#elif __ANDROID__
namespace Authy.Droid.AccountManagement
#endif
{
	public class AccountManagementImplementation : IAccountManagerService
	{
		public List<Services> Accounts
		{
			get
			{
				var accounts = new List<Services>();
				var accountStore = Xamarin.Auth.AccountStore.Create();
				foreach (var service in Enum.GetNames(typeof(Services)))
				{
					var acc = accountStore.FindAccountsForService(service).FirstOrDefault();
					if (acc != null)
					{
						accounts.Add((Services)Enum.Parse(typeof(Services), service));
					}
				}
				return accounts;
			}
		}

		public void EraseAll()
		{
			var accountStore = Xamarin.Auth.AccountStore.Create();
			foreach (var service in Enum.GetNames(typeof(Services)))
			{
				var acc = accountStore.FindAccountsForService(service).FirstOrDefault();
				if (acc != null)
				{
					accountStore.Delete(acc, service);
				}
			}
		}

		public string GetPropertyFromAccount(Services service, string property)
		{
			var accountStore = Xamarin.Auth.AccountStore.Create();
			var account = accountStore.FindAccountsForService(service.ToString()).FirstOrDefault();
			if (account != null)
			{
				return account.Properties[property];
			}
			return null;
		}
	}
}
```  

Para esta ocasión estaremos usando otra de las herramientas de XF, el <a href="https://developer.xamarin.com/guides/xamarin-forms/dependency-service/" target="_blank">DependencyService</a>, así que no te olvides de colocar la siguiente línea en tu archivo:

```csharp  
[assembly: Xamarin.Forms.Dependency(typeof(AccountsImplementation))]
```  

Después, es momento de implementar el *custom renderer* de la página con la que vamos a solicitar la autorización: 

```csharp  
using System;
using Authy.AccountManagement;
using Xamarin.Forms;
using Xamarin.Auth;
#if __IOS__
using Xamarin.Forms.Platform.iOS;
#elif __ANDROID__
using Xamarin.Forms.Platform.Android;
#endif

#if __IOS__
[assembly: ExportRenderer(typeof(AuthorizePage), typeof(Authy.iOS.AccountManagement.AuthorizePageRenderer))]
namespace Authy.iOS.AccountManagement
#elif __ANDROID__
[assembly: ExportRenderer(typeof(AuthorizePage), typeof(Authy.Droid.AccountManagement.AuthorizePageRenderer))]
namespace Authy.Droid.AccountManagement
#endif
{
	public class AuthorizePageRenderer : PageRenderer
	{

#if __IOS__
		public override void ViewDidAppear(bool animated)
		{
			base.ViewDidAppear(animated);
#elif __ANDROID__
		protected override void OnElementChanged(ElementChangedEventArgs<Page> e)
		{
			base.OnElementChanged(e);
			var activity = this.Context as Android.App.Activity;
#endif


			OAuth2Authenticator auth2 = null;
			OAuth1Authenticator auth1 = null;
			IKeys keys = null;

			var authPage = Element as AuthorizePage;

			switch (authPage.Service)
			{
				case Services.Facebook:
					keys = new FacebookKeys();
					auth2 = new OAuth2Authenticator(
						clientId: keys.ClientId,
						scope: keys.Scope,
						authorizeUrl: new Uri(keys.AuthorizeUrl),
						redirectUrl: new Uri(keys.RedirectUrl));
					break;
				case Services.Twitter:
					keys = new TwitterKeys();
					auth1 = new OAuth1Authenticator(
							consumerKey: keys.ConsumerKey,
							consumerSecret: keys.ConsumerSecret,
							requestTokenUrl: new Uri(keys.RequestTokenUrl),
							authorizeUrl: new Uri(keys.AuthorizeUrl),
							accessTokenUrl: new Uri(keys.AccessTokenUrl),
							callbackUrl: new Uri(keys.CallbackUrl));
					break;
				case Services.GitHub:
					keys = new GitHubKeys();
					auth2 = new OAuth2Authenticator(
						clientId: keys.ClientId,
						clientSecret: keys.ClientSecret,
						scope: keys.Scope,
						authorizeUrl: new Uri(keys.AuthorizeUrl),
						redirectUrl: new Uri(keys.RedirectUrl),
						accessTokenUrl: new Uri(keys.AccessTokenUrl));
					break;
				default:
					throw new Exception("Service " + authPage.Service + " not yet supported");
			}

			if (auth2 != null)
			{
				auth2.Completed += async (sender, eventArgs) =>
				{
#if __IOS__
					DismissViewController(true, null);
#endif
					if (eventArgs.IsAuthenticated)
						AccountStore.Create().Save(eventArgs.Account, authPage.Service.ToString());
					await authPage.Navigation.PopAsync();
				};
#if __IOS__
				PresentViewController(auth2.GetUI(), true, null);
#elif __ANDROID__
				activity.StartActivity(auth2.GetUI(activity));
#endif
			}

			if (auth1 != null)
			{
				auth1.Completed += async (sender, eventArgs) =>
				{
#if __IOS__
					DismissViewController(true, null);
#endif
					if (eventArgs.IsAuthenticated)
						AccountStore.Create().Save(eventArgs.Account, authPage.Service.ToString());
					await authPage.Navigation.PopAsync();
				};
			
#if __IOS__
				PresentViewController(auth1.GetUI(), true, null);
#elif __ANDROID__
				activity.StartActivity(auth1.GetUI(activity));
#endif
			}

		}
	}

}
```  

Lo que el código anterior hace es declarar dos formas de obtener la autorización (`OAuth2Authenticator` y `OAuth2Authenticator`), también delcara una variable del tipo `IKeys`. 

Luego obtiene una referencia a la página de Xamarin.Forms desde la que estamos llamándola (para saber a qué servicio queremos conectarnos), con esta información comienza a crear la petición adecuada de acuerdo al servicio que solicitamos. Es importante que veas que dentro de cada una de las opciones de la instrucción `switch`, se instancia la clase que contiene las llaves del servicio que vamos a usar.

Por último, y después de que se ha creado correctamente una instancia de `OAuth2Authenticator` o de `OAuth1Authenticator`, asigna un manejador al evento `Completed`, para que si se obtuvo el permiso requerido lo almacene en el dispositivo mediante la llamada a `AccountStore.Create().Save()`. Luego de todo esto, podemos mostrar la pantalla al usuario. En android mediante el método `StartActivity`, mientras que en iOS mediante `PresentViewController`.

De este modo, cada vez que naveguemos hacia una página del tipo `AuthorizePage`, se mostrará la interfaz de autorización solicitada, de acuerdo al valor pasado en el constructor de la clase.

Ese es todo el código que necesitas para los proyectos "cliente". Ahora, de vuelta al proyecto de Forms:  
   
## De vuelta en Xamarin.Forms  
Cuando solicitemos permiso para usar determinado servicio, lo único que debemos hacer es crear una nueva página en Forms y navegar hacia ella:

```csharp  
await Navigation.PushAsync(new AuthorizePage(Services.Facebook));
```  

Para corroborar si el usuario ha otorgado permiso debemos obtener las cuentas que tenemos guardadas en el dispositivo usando la implementación de la clase `IAccountManagerService` del servicio de dependencias:

```csharp  
_services = DependencyService.Get<IAccountManagerService>();
```  

Y luego revisar la propiedad `Accounts` buscando si existe el servicio que deseamos:

```csharp  
var accounts = _services.Accounts;
if (accounts.Contains(Services.Facebook))
{ // ...
```  

Si sabemos que el usuario nos ha autorizado, podemos acceder a los tokens, claves y demás información de permisos mediante el método `GetPropertyFromAccount`:

```csharp  
var screen_name = _services.GetPropertyFromAccount(Services.Twitter, "screen_name");
var oauth_consumer_key = _services.GetPropertyFromAccount(Services.Twitter, "oauth_consumer_key");
var oauth_token_secret = _services.GetPropertyFromAccount(Services.Twitter, "oauth_token_secret");
```  

## Demo
Puedes ver el vídeo de la app que usa el código explicado en el código anterior:  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="//i.imgur.com/7tBrHtI.gif" />
</div>
<div class="pure-u-1 pure-u-md-1-2">
<img src="http://i.giphy.com/11S78bw1xcfodi.gif" />
</div>  
</div> 


## Para finalizar  
Como puedes ver no es tan complicado (espero me haya explicado bien), y añadir otros servicios es tan simple como conseguir las llaves, modificar unas cuantas clases y ¡listo! Tu app se puede conectar con el resto del internet.  

No olvides que me puedes preguntar dudas por correo o por Twitter, ah... y <a href="https://github.com/fferegrino/Authy" target="_blank"><strong>descargar el código fuente</strong></a>.