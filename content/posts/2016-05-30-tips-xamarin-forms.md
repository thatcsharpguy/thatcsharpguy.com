---
layout: post
title: Tips de desempeño en Xamarin Forms
date: 2016-05-30 08:00:00
author: Antonio Feregrino
excerpt: Jason Smith, ingeniero a cargo de Xamarin Forms, hizo una fantástica presentación en la conferencia Evolve de este año. En ella, él repasó una larga lista de tips de desempeño, así como una sesión de preguntas y respuestas sobre Xamarin Forms.
featured_image: tips.png
images_folder: /xamarin-forms/
tweet_id: 737455093256048641
lang: es
tags: XamarinForms, Xamarin
featured_tag: XamarinForms
---

Esta es la traducción de <a href="http://kent-boogaart.com/blog/jason-smith's-xamarin-forms-performance-tips" target="_blank">un post de Kent Boogaart</a> en donde recopiló una serie de tips sobre cómo mejorar el desempeño de apps hechas con Xamarin Forms.

<hr />
<br />

Jason Smith, ingeniero a cargo de Xamarin Forms, hizo una <a href="https://www.youtube.com/watch?v=RZvdql3Ev0E" target="_blank" rel="nofollow">fantástica presentación</a> en la conferencia Evolve de este año. En ella, él repasó una larga lista de tips de desempeño, así como una sesión de preguntas y respuestas. No he podido encontrar estos tips por escrito (en diapositivas, transcripción o post en blog). Con el interés de hacer esta información más fácilmente accesible para mi y otros, decidí resumirlos aquí.

Me tomé la libertad de reacomodar y agrupar tips relacionados, pero quiero dejar en claro que este es el contenido de Jason, no el mío.

## General  
**HABILITA** la compilación de XAML si estás usándolo para tus vistas:

```csharp  
[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```  

**NO USES** *bindings* cuando asignaciones estáticas sean suficientes.

**NO** asignes valores por default. Hacerlo conlleva un costo, aunque nunca modifiques el valor.

**EVITA** usar transparencias. Busca lograr efectos similares con la opacidad al 100%.

**USA** `async` / `await` para evitar bloquear el hilo principal.

**CONSIDERA** crear los controles en un hilo distinto al principal, pero asegurate de agregarlos al *árbol visual* en el hilo principal. De no hacerlo no hará que tu app falle inmediatamente, pero corromperá su estado. Se especialmente cuidadoso si estás usando `MessagingCenter` en el constructor de tu control.

## Layout
**NO USES** un `ContentView` con `Padding` solo para aplicarle un margen al hijo. Usa, en su lugar, la propiedad `Margin` en el hijo (disponible desde Xamarin.Forms 2.2).

**NO USES** un `StackLayout` para contener un solo hijo.

**NO USES** un `Grid` cuando con `StackLayout` basta.

**NO USES** múltiples `StackLayout`s cuando con un `Grid` es suficiente.

**TOMA EN CUENTA** las propiedades `Spacing` (`ColumnSpacing` / `RowSpacing` en `Grid`) y `Padding`. En lugar de esto:

```xml  
<StackLayout>
    <ContentView Padding="10,10,10,5">
        <Label Text="1"/>
    </ContentView>
    <ContentView Padding="10,0,10,5">
        <Label Text="2"/>
    </ContentView>
    <ContentView Padding="10,0,10,0">
        <Label Text="3"/>
    </ContentView>
</StackLayout>
```  

Haz esto:

```xml  
<StackLayout Padding="10" Spacing="5">
    <Label Text="1"/>
    <Label Text="2"/>
    <Label Text="3"/>
</StackLayout>
```  

**PREFIERE** usar `LayoutOptions.Fill` (o `LayoutOptions.FillAndExpand`). Estos son los valores por default y la *mayoría* de las veces no deben ser modificados.

**PREFIERE** filas/columnas de `Grid` con tamaño definido con `*` en lugar de `Auto`.

**NO USES** múltiples `StackLayout`s para simular un `Grid`.

**NO USES** `RelativeLayout` si es posible.

**AÑADE** agrega los controles hijos a su contenedor padre antes de agregar el padre a la pantalla.

En lugar de esto:

```csharp  
var page = new ContentPage();
var stackLayout = new StackLayout();
var image = new Image { Source = "person.png" };
var label = new Label { Text = "Name" };

page.Content = stackLayout;

navigationPage.Push(page);
stackLayout.Children.Add(image);
stackLayout.Children.Add(label);
```  

Haz esto:

```csharp  
var page = new ContentPage();
var stackLayout = new StackLayout();
var image = new Image { Source = "person.png" };
var label = new Label { Text = "Name" };

stackLayout.Children.Add(image);
stackLayout.Children.Add(label);
page.Content = stackLayout;

navigationPage.Push(page);
```  

XAML ya agrega los controles en el orden *correcto*.

**AÑADE** los controles a la página en el constructor en lugar de en el método `OnAppearing`.

**PREFIERE** animar los controles usando las propiedades `TranslationX` y `TranslatonY`.

**NO** llames al método `Layout()` (y especialmente `ForceLayout()`).

**CONSIDERA** crear un *custom layout*. Es la mejor opción si es fácil describir el orden de tus controles con palabras pero difícil de implementarlo con los *layouts* que vienen en forms, si `AbsoluteLayout` hace *casi* lo que te gusta, o si simplemente necesitas velocidad pura.

**NO USES** un `StackLayout` dentro de un `ScrollView` para simular un `ListView`.

**USA** un `Grids` para hacer ordenamiento por capas.

## Labels
**NO USES** múltiples `Label`s cuando con una (usando *spans* con `FormattedText` cuando es necesario) basta.

**DESACTIVA** `Label` *wrapping* si es posible (`LineBreakMode="NoWrap"`).

**NO** cambies el valor de `VerticalTextAlignment` a menos que sea necesario. El valor por default elimina un ciclo de medición completo.

**PREFIERE** las propiedades `VerticalTextAlignment` y `HorizontalTextAlignment` sobre `VerticalOptions` y `HorizontalOptions`.

**EVITA** actualizaciones esporádicas a `Label`s. Si las actualizaciones son a múltiples `Label`s, agrupa las actualizaciones de ser posible.

## ListViews

**NO** pongas un `ListView` dentro de un `ScrollView`. Usa las propiedades `Header` y `Footer` en su lugar.  

**NO USES** `TableView` donde puedas usar `ListView`. Hoy en día, la única razón real para usar un `TableView` es por interfaces del estilo de un panel de configuración en el que cada celda tiene un contenido único.

**USA** `ListViewCachingStrategy.RecycleElement` cuando puedas. Si no te funciona, revisa por qué no lo hace, realmente vale la pena. Debes activar esta propiedad manualmente.

**USA** los selectores de *data template* para facilitar el uso de distintos controles dentro de un solo `ListView`. No sobreescribas `OnBindingContextChanged` para obtener el mismo efecto.

**EVITA** usar `IEnumerable<T>` como origen de datos para un `ListView`, en su lugar, intenta usar `IList<T>`.

**NO** anides `ListView`s, en su lugar utiliza grupos dentro de uno. No hay soporte para anidamiento de `ListViews` y hará que tu aplicación falle.

**USA** la propiedad `HasUnevenRows` cuando tu `ListView` tenga filas de distintos tamaños. Si el contenido de una celda es modificado dinámicamente (tal vez despuésde cargar información de la base de datos), asegúrate de llamar `ForceUpdateSize()` en la celda.

## Navegación
**USA** `await` cuando ejecutes `PushAsync` y `PopAsync`. El no hacerlo reduce el desempeño y la *correctitud* de tu aplicación.

**EVITA** ocultar / mostrar la barra de navegación.

**USA** el <a href="https://gist.github.com/jassmith/a3b2a543f99126782936" target="_blank" rel="nofollow">soporte para AppCompat</a> para Android. Esto mejorará el desempeño y apariencia de la aplicación.

## Imágenes  
**TOMA EN CUENTA** que Android no se optimiza el tamaño de la imagen.

**ASIGNA** `true` a `Image.IsOpaque` si es posible.

**CARGA** imagenes desde *Content* en lugar de *Recursos*.

## BindableProperty
**NO USES** la versión genérica de `BindableProperty.Create` (usa la versión basada en `string` en conjunto con el operador `nameof`).

## CarouselPage

**NO USES** la `CarouselPage`

**USA** un `ContentView` dentro de una `ContentPage

## MessagingCenter
**PREFIERE** usar otro framework (como Prism).

**USA** un método estático o de instancia en lugar de una expresión lambda para el `MessagingCenter`.
