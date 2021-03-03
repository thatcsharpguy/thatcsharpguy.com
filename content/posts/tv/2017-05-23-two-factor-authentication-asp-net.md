---
title:  Autenticación de dos factores (ejemplo en ASP.NET Core)
slug: two-factor-authentication-asp-net
date:  2017-05-23 18:00:00
youtube_id: E4ROr6_dETU
images_folder:  /tv/2fa/
summary:  Aprende a implementar la autenticación de dos factores con ASP.NET Core y Goolge Authenticator.
featured_image:  code-featured.png
tags:  Meta, YouTube
featured_tag:  Tv
layout: video
---

Los pasos a realizar son los siguientes:  

## Creación del proyecto 

 - Crear un nuevo proyecto, debe ser ASP.NET Core Web Application (.NET Core)  
 - Como template hay que seleccionar Web Application
 - Debes cambiar las opciones de autenticación a Individal User Accounts  

## Habilitar SSL  

 - Agregar el paquete de NuGet: Microsoft.AspNetCore.Rewrite
 - Agregar el siguiente código en el archivo Startup.cs, línea 44 

```csharp  
// Enforce SSL
services.Configure<MvcOptions>(options =>
{
	options.Filters.Add(new RequireHttpsAttribute());
});
```  

  - Agregar el siguiente código en el archivo Startup.cs, línea 81


```csharp  
// Requires using Microsoft.AspNetCore.Rewrite;
var options = new RewriteOptions()
   .AddRedirectToHttps();
```  

 - Habilitar SSL en las propiedades del sitio web, en la pestaña de debug. 

## Almacenar un código único para cada usuario  

 - Agregar el siguiente código en el archivo ApplicationUser.cs, línea 12


```csharp  
public virtual string TfaKey { get; set; }
```  

 - Agregar el siguiente código en el archivo 00000000000000_CreateIdentitySchema.cs, línea 60


```csharp  
TfaKey = table.Column<string>(maxLength: 32, nullable: true),
```  

## Preparar la aplicación para que soporte sesiones

 - Agregar paquete de NuGet: Microsoft.AspNetCore.Session  
 - Agregar el siguiente código en el archivo Startup.cs, línea 60


```csharp  
// Add session support
services.AddSession(options =>
{
	// Set a short timeout for easy testing.
	options.IdleTimeout = TimeSpan.FromSeconds(10);
	options.CookieHttpOnly = true;
});
```  

 - Agregar el siguiente código en el archivo Startup.cs, línea 98


```csharp  
app.UseSession();
```  

## Agregar código de Google Authenticator  

Código de <a href="http://brandonpotter.com/2014/09/07/implementing-free-two-factor-authentication-in-net-using-google-authenticator/" target="_blank">Brandon Potter</a>.  

 - Agregar nuevo proyecto (Google.Authenticator) del tipo Class Library (.NET Core)  
 - Añadir clases: <a href="https://raw.githubusercontent.com/ThatCSharpGuy/DosFactores/master/Google.Authenticator/SetupCode.cs" target="_blank">SetupCode</a> y <a href="https://raw.githubusercontent.com/ThatCSharpGuy/DosFactores/master/Google.Authenticator/TwoFactorAuthentication.cs" target="_blank">TwoFactorAuthentication</a>.  
 - Agregar referencia del nuevo proyecto al sitio web  

## Agregar el proveedor de tokens en el sitio web  

Código de <a href="http://www.domstamand.com/two-factor-authentication-in-asp-net-identity-3-using-totp-authenticator/" target="_blank">Dominique St-Amand</a>.  

 - Agregar clase <a href="https://raw.githubusercontent.com/ThatCSharpGuy/DosFactores/master/DosFactores/Providers/GoogleAuthenticatorProvider.cs" target="_blank">GoogleAuthenticatorProvider</a>.
 - Agregar el siguiente código en el archivo Startup.cs, línea 57


```csharp  
.AddTokenProvider(GoogleAuthenticatorProvider.ProviderName, typeof(GoogleAuthenticatorProvider))
```  

## Preparar la interfaz para el 2FA

 - Agregar el siguiente código en el archivo IndexViewModel.cs, línea 17


```csharp  
public string TwoFactorAuthenticatorQrCode { get; set; }
```  

 - Agregar el siguiente código en el archivo ManageController.cs, líneas 64, 123 y 168


```csharp  
TwoFactorAuthenticatorQrCode = TempData["AuthenticatorQr"]?.ToString(),
```  


```csharp  
// POST: /Manage/RequestTwoFactorAuthentication
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RequestTwoFactorAuthentication()
{
  var user = await GetCurrentUserAsync();
  if (user != null)
  {
	  var tfaKey = Guid.NewGuid().ToString("N");
	  user.TfaKey = tfaKey;
	  await _userManager.UpdateAsync(user);
	  var authenticator = new TwoFactorAuthenticator();
	  var code = authenticator.GenerateSetupCode(user.UserName, tfaKey, 300, 300);
	  TempData["AuthenticatorQr"] = code.QrCodeSetupImageUrl;
	  _logger.LogInformation(1, "User enabled two-factor authentication.");
  }
  return RedirectToAction(nameof(Index), "Manage");
}
```  

```csharp  
user.TfaKey = null;
await _userManager.UpdateAsync(user);
```  

20. Reemplazar el código del `<dd></dd>` de la autenticación de dos factores por 


```csharp  
@if (Model.TwoFactorAuthenticatorQrCode != null)
{
	<img src="@Model.TwoFactorAuthenticatorQrCode" />
	<form asp-controller="Manage" asp-action="EnableTwoFactorAuthentication" method="post" class="form-horizontal">
		<button type="submit" class="btn-link btn-bracketed">Yes, I've scanned the code</button> Disabled
	</form>
}
else
{
	if (Model.TwoFactor)
	{
		<form asp-controller="Manage" asp-action="DisableTwoFactorAuthentication" method="post" class="form-horizontal">
			Enabled <button type="submit" class="btn-link btn-bracketed">Disable</button>
		</form>
	}
	else
	{
		<form asp-controller="Manage" asp-action="RequestTwoFactorAuthentication" method="post" class="form-horizontal">
			<button type="submit" class="btn-link btn-bracketed">Enable</button> Disabled
		</form>
	}
}
```  

## Listo 

