---
layout: post
title: Tabs más familiares a tus usuarios en Xamarin.Forms
date: 2017-01-31 19:00:01
author: Antonio Feregrino
summary: Dale más estilo a tus páginas de tabs en Xamarin.Forms, haz que se vean más familiares a tus usuarios sin que pierdan el estilo y los colores de tu marca y como bonus practica haciendo un custom renderer.
featured_image: featured.jpg
images_folder: /xamarin-forms/tabbed-page/
github: https://github.com/messier16/PlatformTabbedPage
lang: es
tags: Xamarin, XamarinForms, NuGetRecomendado
featured_tag: XamarinForms
---

Entre la gran variedad de páginas pre-cargadas que nos ofrece Xamarin.Forms tenemos la opción de elegir la `TabbedPage` que en realidad no es más que un contenedor de otras páginas y que las hace accesibles a través de *tabs*. Seguramente la has usado y piensas que es genial, lo que no es tan genial es que es un poco... estática y no permite mucha personalización.  

Es por eso que decidí crear un <a href="https://developer.xamarin.com/guides/xamarin-forms/custom-renderer/" target="_blank">custom renderer</a> que permita darle un poco más de estilo, haciendo que se vea más nativa para tus usuarios.  

## Uso  
Para comenzar a usar estas tabs tienes dos opciones: instalar el <a href="https://www.nuget.org/packages/PlatformTabbedPage/" target="_blank">paquete de NuGet</a> en tus tres proyectos o descargar el <a href="https://github.com/messier16/PlatformTabbedPage" target="_blank">código fuente</a> y colocarlo en tu aplicación.  

```  
PM> Install-Package PlatformTabbedPage
```  

Después, tienes que crear una página que derive de `PlatformTabbedPage`:  

```csharp  
public class HomeTabbedPage : PlatformTabbedPage
```  

Y como normalmente haces con las `TabbedPage` originales, añadir las páginas hijo en el constructor. Es **muy importante** que notes cómo es que el ícono no termina en ".png", ya que de otro modo las tabs no funcionarán correctamente en iOS:  

```csharp  
    public HomeTabbedPage()
    {
        BarBackgroundColor = App.BarBackgroundColors[3];
        SelectedColor = App.SelectedColors[0];
        BarBackgroundApplyTo = BarBackgroundApplyTo.None;

        Children.Add(new ConfigurationPage { Icon = "feed" });
        Children.Add(new BasicContentPage("YouTube") { Icon = "youtube" });
        Children.Add(new BasicContentPage("Twitter") { Icon = "twitter" });
        Children.Add(new BasicContentPage("Info") { Icon = "info" });
    }
}
```  

Y obtendremos algo como esto (del lado izquierdo es la app creada con una `TabbedPage` tradicional):

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/tabbed-page/android-no-small.gif" title="iOS no normal" />
</div>
<div class="pure-u-1 pure-u-md-1-2">  
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/tabbed-page/android-small.gif" title="iOS no normal" />
</div>  
</div> 

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">  
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/tabbed-page/ios-no-small.gif" title="iOS no normal" />
</div>
<div class="pure-u-1 pure-u-md-1-2">  
<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms/tabbed-page/ios-small.gif" title="iOS no normal" />
</div>  
</div>  

Descarga el <a href=" https://github.com/messier16/PlatformTabbedPage" target="_blank">código fuente</a> para que puedas ver el ejemplo de este post.  

Y ahora, si quieres saber cómo es que logré esta personalización, sigue leyendo.


## PlatformTabbedPage  
Entre las cosas que quise personalizar están el color del elemento seleccionado, el color del elemento deseleccionado, y en iOS el ícono del elemento seleccionado, ya que como viste en las imagenes anteriores en Android resulta un poco complicado resolver a primera vista qué elemento estamos viendo, mientras que en iOS resulta un poco extraño que el ícono del elemento seleccionado permanezca igual que los otros.

### Creación del renderer  
Comenzamos por definir una clase que derive del tipo de página que vamos a personalizar  

```csharp  
public class PlatformTabbedPage : TabbedPage
```  

Luego definimos las propiedades, con su respectiva `BindableProperty` para permitir que se establezcan *bindings* a ellas: 

```csharp  
public static readonly BindableProperty SelectedColorProperty =
    BindableProperty.Create(nameof(SelectedColor), typeof(Color), typeof(PlatformTabbedPage), default(Color));

public Color SelectedColor
{
    get { return (Color)GetValue(SelectedColorProperty); }
    set { SetValue(SelectedColorProperty, value); }
}

public static readonly new BindableProperty BarBackgroundColorProperty =
    BindableProperty.Create(nameof(BarBackgroundColor), typeof(Color), typeof(PlatformTabbedPage), default(Color));

public new Color BarBackgroundColor
{
    get { return (Color)GetValue(BarBackgroundColorProperty); }
    set { SetValue(BarBackgroundColorProperty, value); }
}

public static readonly BindableProperty BarBackgroundApplyToProperty =
    BindableProperty.Create(nameof(BarBackgroundApplyTo), typeof(BarBackgroundApplyTo), typeof(PlatformTabbedPage), BarBackgroundApplyTo.Android);

public BarBackgroundApplyTo BarBackgroundApplyTo
{
    get { return (BarBackgroundApplyTo)GetValue(BarBackgroundApplyToProperty); }
    set { SetValue(BarBackgroundApplyToProperty, value); }
}
```  

La descripción de las propiedades es la siguiente:  
 
 - `SelectedColor`: El color del elemento seleccionado
 - `BarBackgroundColor`: El color de fondo de la barra, decidí ocultar con `new` la implementación por default, ya que voy a controlar el comportamiento de esta propiedad dentro de cada renderer  
 - `BarBackgroundApplyTo`: Una <a href="..\c-sharp-enums" target="_blank"><i>enumeración</i></a> que indica en qué plataformas modificaremos el color de la barra, y es que en mi opinión en iOS es un poco "antinatural" cambiar el color de la barra.   

Además cree unos <a href="..\extension-methods-es" target="_blank">métodos de extensión</a> para trabajar con los colores, haciéndolos más claros o más oscuros además de uno que ayuda a decidir si un color es claro u obscuro, puedes encontrar más detalles en el código fuente.  
Ahora sí, vamos a ver la implementación en cada plataforma:  

## iOS  
(para este *renderer* me basé por completo en la <a href="https://github.com/xamarinhq/app-evolve/blob/master/src/XamarinEvolve.iOS/Renderers/SelectedTabPageRenderer.cs" target="_blank">implementación</a> de la app Evolve de Xamarin)  

En iOS vamos a crear un *renderer* que derive de `TabbedRenderer`:  

```csharp  
public class PlatformTabbedPageRenderer : TabbedRenderer
```  

Después definimos unas cuantas propiedades una para acceder al la instancia en forms de `PlatformTabbedPage` y otro par para almacenar los colores por default de la `UITabBar` original:  

```csharp  
PlatformTabbedPage FormsTabbedPage => Element as PlatformTabbedPage;
UIColor DefaultTintColor;
UIColor DefaultBarBackgroundColor;
```  

Si alguna vez has escrito un *custom renderer* ya sabrás que el método `OnElementChanged` es llamado cada vez que se va a renderizar en pantalla el control, es por eso que dentro de él asignamos (y desasignamos) un evento que nos permitirá estar al tanto de cuando las propiedades de la barra cambien en el proyecto de forms.

```csharp  
if (e.OldElement != null)
{
    e.OldElement.PropertyChanged -= OnElementPropertyChanged;
}
if (e.NewElement != null)
{
    e.NewElement.PropertyChanged += OnElementPropertyChanged;
}
```  

Además de recuperar los colores por default del TabBar en caso de que los necesitemos más adelante.

```csharp  
DefaultTintColor = TabBar.TintColor;
DefaultBarBackgroundColor = TabBar.BackgroundColor;
```  

Una vez hecho esto llamamos a un par de métodos que nos servirán para asignar los colores que deseamos.

```csharp  
SetTintedColor();
SetBarBackgroundColor();
```  

Ahora toca el turno de "rellenar" los íconos, y digo "rellenar" porque en realidad es necesario que hayas creado un par de íconos por cada tab, uno que se muestre por default y otro que se muestre cuando la tab está seleccionada.  

El truco aquí radica en que la API de iOS permite establecer dos imágenes una seleccionada y otra deseleccionada, y eso es precisamente lo que hace este renderer: toma el ícono, le añade el sufijo "_active" y lo coloca como la imagen que debe mostrarse cuando el elemento está seleccionado:  

```csharp  
if (FormsTabbedPage != null)
{
    for (int i = 0; i < TabBar.Items.Length; i++)
    {
        var item = TabBar.Items[i];
        var icon = FormsTabbedPage.Children[i].Icon;

        if (item == null)
            return;
        try
        {
            icon = icon + "_active";
            if (item?.SelectedImage?.AccessibilityIdentifier == icon)
                return;
            item.SelectedImage = UIImage.FromBundle(icon);
            item.SelectedImage.AccessibilityIdentifier = icon;
        }
        catch (Exception ex)
        {
            Console.WriteLine("Unable to set selected icon: " + ex);
        }
    }
}
```  

Y básicamente eso es todo, no se requiere de más gracias a la API que nos otorga iOS y a la la propiedad `SelectedImage`.

## Android  
Para Android el *renderer* del que debemos derivar es `TabbedPageRenderer`... sí, distinto al de iOS:  

```csharp  
public class PlatformTabbedPageRenderer : TabbedPageRenderer
```  

Se necesitan unas cuantas propiedades para preservar los colores por default:

```csharp  
PlatformTabbedPage FormsTabbedPage => Element as PlatformTabbedPage;
AndroidColor _selectedColor = AndroidColor.Black;
AndroidColor DefaultUnselectedColor = FormsColor.Gray.Darken().ToAndroid();
static AndroidColor BarBackgroundDefault;
AndroidColor _unselectedColor = DefaultUnselectedColor;
```  

En Android una página de pestñas está compuesta normalmente por un par de elementos independientes: un `ViewPager` y un `TabLayout` que se coordinan entre ellos para funcionar como un solo control, al ser parte central de este *renderer*  también necesitamos tener una referencia a ellos a nivel de clase:  

```csharp  
ViewPager _viewPager;
TabLayout _tabLayout;
```  

Ahora, dentro del importantísimo método `OnElementChanged` obtendremos las referencias el par de controles declarados arriba. El renderer contiene ambos, sin embargo no los tiene directamente accesibles si no que son del tipo `View`, es por eso que se usa `is` para identificarlos y después hacer el *cast* de manera segura:

```csharp  
for (int i = 0; i < ChildCount; i++)
{
    var v = GetChildAt(i);
    if (v is ViewPager)
        _viewPager = (ViewPager)v;
    else if (v is TabLayout)
        _tabLayout = (TabLayout)v;
}
```  

Obtenemos el color por default y establecemos los colores de la barra:   

```csharp  
BarBackgroundDefault = (_tabLayout.Background as ColorDrawable)?.Color ?? Android.Graphics.Color.Green;
SetSelectedColor();
SetBarBackgroundColor();
```  

A diferencia de iOS, en Android tendremos que hacer uso de un par de métodos que son llamados cada vez que se selecciona/deselecciona una *tab*, es por eso que también son asignados dentro de el método principal:

```csharp  
_tabLayout.TabSelected += TabLayout_TabSelected;
_tabLayout.TabUnselected += TabLayout_TabUnselected;
```  

Por último establecemos el color de todos los íconos en la barra de pestañas, y marcamos cono seleccionado el primer elemento mediante un pequeño método auxiliar:  

```csharp  
SetupTabColors();
SelectTab(0);
```  

Ahora si, la "magia" de los métodos que son llamados cuando se slecciona o deselecciona una pestaña: básicamente consiste en aplicar un filtro de color al ícono de la tab seleccionada, y dependiendo de qué método se haya llamado aplicar el color adecuado:  

```csharp  
private void TabLayout_TabUnselected(object sender, TabLayout.TabUnselectedEventArgs e)
{
    var tab = e.Tab;
    tab.Icon?.SetColorFilter(_unselectedColor, PorterDuff.Mode.SrcIn);
}

private void TabLayout_TabSelected(object sender, TabLayout.TabSelectedEventArgs e)
{
    var tab = e.Tab;
    tab.Icon?.SetColorFilter(_selectedColor, PorterDuff.Mode.SrcIn);
}
```  

Y eso es todo... básicamente el renderer de Android es un poco más complicado que el de iOS, pero nada que sea del otro mundo.  

