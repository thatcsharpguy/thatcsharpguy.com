---
layout: post
title: Otro tutorial sobre menú de hamburguesa
date: 2017-08-01 19:00:01
author: Antonio Feregrino
excerpt: Aprende una forma (sí, otra) de implementar un menú de hamburguesa en Xamarin.Forms para organizar mejor al información dentro de tu aplicación.
featured_image: featured.png
images_folder: /xamarin-forms/hamburger/
github: https://github.com/ThatCSharpGuy/MenuApp/
lang: es
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

Sí, en efecto, otro post sobre cómo crear un menú de hamburguesa en Xamarin.Forms, probablemente este sea como el millonésimo artículo al respecto. *Sin embargo, esta es mi forma, hay muchas otras, pero esta es la mía*.

Precaución, post largo a continuación. No olvides que puedes descargar <a href="https://github.com/ThatCSharpGuy/MenuApp/" target="_blank">aquí el código</a>, por si entre tanto te llegas a perder. Además, no dudes en hacerme llegar cualquier comentario.

## `MasterDetailPage`

En este post vamos a usar una `MasterDetailPage` para crear una app con un menú que se deslice del lado izquierdo de la pantalla, así que con eso vamos a comenzar con eso. Crea una página que herede de esta clase, yo le he puesto como nombre `MainPage`:

```csharp  
public class MainPage : MasterDetailPage
{
```  

Luego, dentro del constructor de esta clase debes establecer las propiedades `Master` y `Detail`, para nosotros `Master` será la página en la que se muestre el menú y `Detail` en donde se muestre el contenido de la app. Por el momento solamente vamos establecerla dos páginas genéricas, sin nada de significado para la app:

```csharp  
public MainPage()
{
    Master = new ContentPage
    {
        Title= "Master page",
        Content = new StackLayout
        {
            Children =
            {
                new Label{ Text = "Master page" }
            }
        }
    };

    Detail = new ContentPage
    {
        Title = "Detail page",
        Content = new StackLayout
        {
            Children =
            {
                new Label{ Text = "Detail page" }
            }
        }
    };
}
```  

Asegúrate de que esta página esté establecida como `MainPage` en la clase `App` de tu proyecto de Forms.

Ahora, para fines de este proyecto vamos a crear unas cuantas páginas que sí tendrán relación con la lógica de nuestra app, no voy a poner el código completo aquí, pero la app mostrará los gimnasios pokémon de la liga Índigo, los miembros de la Élite 4 y los distintos tipos de pokémon, así como detalles para cada una de ellas. Pero toma en cuenta, *las páginas principales son `GymsPage`, `EliteFourPage` y `PokemonTypesPage`*.

## Construcción del menú  

### `MenuListItem`  

Nuestro menú será una simple lista cuyas filas harán la función de botones. Cada uno de estos "botones" estará representado por la clase `MenuListItem` cuya definición es la siguiente:

```csharp  
public class MenuListItem
{
    public string Title { get; set; }
    public string Icon { get; set; }
    public Type Page { get; set; }
}
```  

Las dos primeras propiedades son sencillas de identificar (el título del menú y la imagen asociada a este), sin embargo, la tercera no lo es tanto: esta debe ser el tipo de página que se debe mostrar cuando el usuario seleccione esta opción, más adelante veremos su uso.  

### `MenuListView`

Ahora, mientras que podríamos usar un `ListView` directamente, vamos a usar un derivado, por si en algún momento necesitamos añadirle funcionalidad o crear un <a href="..\custom-renderer-paint-code-es" target="_blank"><i>custom renderer</i></a> de esta para mostrar los ítems que creamos anteriormente, entonces crea una clase llamada `MenuListView`:

```csharp  
public class MenuListView : ListView
{
```  

Y en el constructor de esta clase crea los ítems que formarán parte del menú y establécelos como la propiedad `ItemsSource`. Acá es donde se verá el uso de la propiedad `Page`:

```csharp  
public MenuListView()
{
    var menus = new List<MenuListItem>
    {
        new MenuListItem
        {
            Title = "Gimnasios",
            Icon = "gym.png",
            Page = typeof(GymsPage)
        },
        new MenuListItem
        {
            Title = "Elite 4",
            Icon = "elite.png",
            Page = typeof(EliteFourPage)
        },
        new MenuListItem
        {
            Title = "Tipos pokemon",
            Icon = "types.png",
            Page = typeof(PokemonTypesPage)
        }
    };
    ItemsSource = menus;
}
```  

### `MenuItemCell`
Para mostrar los menús estaremos utilizando una `ImageCell`, pero, al igual que con la `MenuListView` usaremos una clase derivada por si en el futuro debemos añadirle funcionalidad:  

```csharp  
public class MenuListItemCell : ImageCell
{
}
```  

Ahora, en el constructor de nuestra lista, estableceremos el *template* de las celdas para que utilice este nuevo que acabamos de crear:

```csharp  
ItemsSource = menus;

var cellTemplate = new DataTemplate(typeof(MenuListItemCell));
cellTemplate.SetBinding(TextCell.TextProperty, nameof(MenuListItem.Title));
cellTemplate.SetBinding(ImageCell.ImageSourceProperty, nameof(MenuListItem.Icon));

ItemTemplate = cellTemplate;
```  

### `SideMenuPage`
Listo hasta ahora hemos creado ya casi toda la infraestructura necesaria para echar a andar nuestro menú: tenemos las opciones de menú, la lista en la que se mostrarán y la celda en la que lo harán, pero nos falta un lugar en donde ponerlos. Para esto, crea una clase llamad `SideMenuPage`, que será una página como cualquier otra:

```csharp  
public class SideMenuPage : ContentPage
{
```  

Y dentro de su constructor debes crear la apariencia con la que debe contar tu menú, aquí es donde se crea y se muestra, ahora sí, la lista de menús:  

```csharp  
public SideMenuPage()
{
    Title = "Pokémon";
    var containerGrid = new Grid
    {
        RowDefinitions =
        {
            new RowDefinition {Height = new GridLength(1, GridUnitType.Auto)},
            new RowDefinition {Height = new GridLength(1, GridUnitType.Star)}
        }
    };
    var titleLabel = new Label()
    {
        FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
        Text = Title, Margin = 10,
        HorizontalTextAlignment = TextAlignment.Center        
    };

    var menuList = new MenuListView();
    containerGrid.Children.Add(titleLabel);
    containerGrid.Children.Add(menuList, 0, 1);
    Content = containerGrid;
}
```  

Y ahora la casi culminación de todos nuestros esfuerzos. Dentro de tu `MainPage` (la que deriva del *masterdetail*) establece la propiedad master a una instancia de `SideMenuPage`:  

```csharp  
var sideMenuPage = new SideMenuPage();
Master = sideMenuPage;
```  

Ahora, podrías ejecutar tu aplicación y verla en "casi" todo su esplendor.

## Añadiendo la navegación  
Si ejecutaste tu app verás que básicamente no hace nada. Pero no te preocupes, que vamos a cambiar eso justo ahora. 

### Selección del menú  
Ahora toca añadir la posibilidad de saber qué ítem del menú fue presionado, para que nuestro programa reaccione de acuerdo a la opción del menú seleccionada. Entonces, dentro de la clase `SideMenuPage` añade un evento: 

```csharp  
public event EventHandler<MenuListItem> MenuItemSelected;
```  

Y a la lista de ítems agrégale un manejador a su evento `ItemSelected`:

```csharp  
var menuList = new MenuListView();
menuList.ItemSelected += InternalMenuItemSelected;
```  

Dentro del manejador deberás invocar el evento que justamente acabas de añadir llamado `MenuItemSelected`, pasándole como argumento el menú seleccionado:

```csharp  
private void InternalMenuItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    var menuItem = e.SelectedItem as MenuListItem;
    if (menuItem != null)
    {
        MenuItemSelected?.Invoke(this, menuItem);
        ((MenuListView) sender).SelectedItem = null;;
    }
}
```  

Ahora, vamos a hacer uso de estas herramientas que acabamos de añadir, dentro del constructor de la clase `MainPage` añade un manejador a nuestra `sideMenuPage`:

```csharp  
sideMenuPage.MenuItemSelected += SideMenuPage_MenuItemSelected;
```  

Dentro de este manejador es donde se llevará a cabo la navegación, por el momento la dejaremos pendiente:  

```csharp  
private void SideMenuPage_MenuItemSelected(object sender, Controls.MenuListItem e)
{
    // Navegación
}
```  

### `MenuNavigationPage`
Para realizar la navegación usaremos los mecanismos que nos ofrece Forms por default, y eso incluye hacer uso de `NavigationPage`, pero nuevamente haremos uso de una clase derivada porque vamos a añadirle un par de eventos que nos ayudarán a saber si es que la página inicial (con la que se creó inicialmente la `NavigationPage`) se está mostrando.

```csharp  
public class MenuNavigationPage : NavigationPage
{
    public MenuNavigationPage(Page root) : base(root)
    {
```  

En este caso el argumento `root` será nuestra página inicial, así que la almacenaremos en un campo llamado `_landingPage`, además le agregaremos un par de manejadores de eventos para saber cuándo esta página aparezca y desaparezca ante los ojos del usuario:

```csharp  
        _landingPage = root;
        _landingPage.Appearing += PageAppearing;
        _landingPage.Disappearing += PageDisappearing;
    }

    private readonly Page _landingPage;
```  

Hasta este punto tenemos manejado el evento dentro de nuestra página, pero ahora debemos hacer disponible un par de eventos que se llamarán cuando aparezca y desaparezca la página inicial: 

```csharp  

    public event EventHandler InitialPageAppearing;
    public event EventHandler InitialPageDisappearing;

    private void PageDisappearing(object sender, EventArgs e)
    {
        InitialPageDisappearing?.Invoke(sender, e);
    }

    private void PageAppearing(object sender, EventArgs e)
    {
        InitialPageAppearing?.Invoke(sender, e);
    }
}
```  

Listo, una vez que tenemos ya una página para hacer la navegación, podemos llevarla a cabo dentro del manejador que dejamos pendiente de implementar. Usamos la clase `Activator` para crear una instancia de la página que vamos a mostrar, la "envolvemos" en una `MenuNavigationPage` y la establecemos como `Detail` de nuestra `MainPage`. Por mediante `IsPresented` le indicamos al menú que se esconda una vez que se ha presentado ya una página nueva:

```csharp  
private void SideMenuPage_MenuItemSelected(object sender, Controls.MenuListItem e)
{
    MenuNavigationPage page;
    var actualContentPage = (Page)Activator.CreateInstance(e.Page);
    page = new MenuNavigationPage(actualContentPage);
    Detail = page;
    IsPresented = false;
}
```  

Y listo, hasta aquí ya debería funcionar todo correctamente, la navegación de tu app funciona y es una maravilla. Sin embargo, quédate para un par de mejoras:  

### Deshabilitando el deslizamiento del menú  
Algo que me desagradaba bastante es que cuando el usuario está en una página que no es una de las *páginas iniciales*  podía abrir el menú y navegar a otra pantalla. Para hacer que el menú aparezca bastaba con deslizar el dedo desde el borde izquierdo de la pantalla. Para nuestra fortuna, podemos deshabilitar característica mediante la propiedad `IsGestureEnabled` de nuestra `MainPage`, pero para esto, a cada una de las páginas que vayamos creando debemos agregarle un par de manejadores para sus eventos de aparición/desaparición de la página inicial:  

```csharp  
    // ...
    page = new MenuNavigationPage(actualContentPage);
    page.InitialPageAppearing += Page_InitialPageAppearing;
    page.InitialPageDisappearing += Page_InitialPageDisappearing;
    Detail = page;
    // ...
```  

Y dentro de dichos manejadores habilitaremos y deshabilitaremos el gesto del menú: 

```csharp  
private void Page_InitialPageDisappearing(object sender, EventArgs e)
{
    IsGestureEnabled = false;
}

private void Page_InitialPageAppearing(object sender, EventArgs e)
{
    IsGestureEnabled = true;
}
```  

### Reusando las páginas (experimental)  
Hasta ahora cada vez que el usuario selecciona una opción del menú se crea una nueva página, entonces es crear y crear recursos nuevos, es por eso que podemos evitar esto creando una especie de almacenamiento para nuestras páginas, y si el usuario selecciona una que ya había abierto con anterioridad podemos presentarle esa, en lugar de crear una nueva. Esto se logra mediante la introducción de un diccionario cuya llave sea el tipo de página y valor sea una instancia de dicho tipo:

```csharp  
private Dictionary<Type, MenuNavigationPage> _pages = new Dictionary<Type, MenuNavigationPage>();
```  

Y ahora si podemos almacenar las páginas en este diccionario y sacarlas de ahí o crearlas y almacenarlas para el futuro: 

```csharp  
private void SideMenuPage_MenuItemSelected(object sender, Controls.MenuListItem e)
{
    MenuNavigationPage page;
    if (!_pages.TryGetValue(e.Page, out page))
    {
        var actualContentPage = (Page)Activator.CreateInstance(e.Page);
        page = new MenuNavigationPage(actualContentPage);
        page.InitialPageAppearing += Page_InitialPageAppearing;
        page.InitialPageDisappearing += Page_InitialPageDisappearing;
        _pages.Add(e.Page, page);
    }
    Detail = page;
    IsPresented = false;
}
```  

(En realidad me hace falta perfilar esta solución y ver si no existe una fuga de memoria por ahí).  

Y pues listo, eso es todo. Sé que podrían quedar un montón de dudas y después de tantas clases derivadas y eventos y *bla, bla, bla*, sin embargo, te invito a que descargues <a href="https://github.com/ThatCSharpGuy/MenuApp/" target="_blank">aquí el código</a> y lo sigas paso a paso, demás, no dudes en ponerte en contacto conmigo.  
