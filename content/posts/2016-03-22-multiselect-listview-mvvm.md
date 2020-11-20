layout: post
title: Multi-select ListView y MVVM
date: 2016-03-23 08:00:00
author: Antonio Feregrino
excerpt: Aprende cómo permitir al usuario selecionar más de un item en una lista y a hacerlo todo desde MVVM con Xamarin.Forms.
featured_image: multiselect-featured.png
images_folder: /xamarin-forms/
github: https://github.com/ThatCSharpGuy/MultiPokeList
tweet_id: 712819064104570880
lang: es
tags: XamarinForms, Xamarin
featured_tag: XamarinForms

Por fin he vuelto a desarrollar algo con Xamarin.Forms y parte de lo que he tenido que hacer tuvo que ver con implementar una lista que permitiera la selección múltiple de filas y fuera compatible con el patrón MVVM.  

Investigando un poco encontré que el control `ListView`, que es el más usado para mostrar datos en forma de lista, no permite la selección múltiple y que habría que hacerlo de otra manera. Dentro de la documentación de Xamarin también <a href="https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/multiselect/" target="_blank" rel="nofollow">este artículo</a> en el que explica una forma de hacerlo y en ese está basado este post.  
  
Para llevar a cabo la tarea, debemos tomar en cuenta que necesitaremos de algunas clases auxiliares que explicaré a detalle más adelante.

### El modelo  
Para este ejemplo vamos a mostrar una lista de Pokemons y permitiremos al usuario seleccionar varios de ellos a través de una lista, la clase de nuestro modelo es la siguiente:

```csharp  
public class Pokemon 
{
    public int Id { get; set; }
    public string Name { get; set; }
    public double Weight { get; set; }
    public double Height { get; set; }
}
```  

### SelectableItemWrapper&lt;T&gt;
Adicionalmente al modelo, también es necesario esta clase auxiliar que como el nombre lo indica, es una envoltura para nuestro modelo, la definición es la siguiente:

```csharp  
public class SelectableItemWrapper<T>
{
    public bool IsSelected { get; set; }
    public T Item { get; set; }
}
```  

La propiedad `IsSelected` servirá para determinar si el usuario seleccionó el ítem o no, mientras que `Item` contendrá el modelo.

## La vista
(If you prefer to work with XAML, <a href="//thatcsharpguy.com/post/multiselect-listview-mvvm-en">see this post</a>)

Usaremos dos páginas de Forms, una donde mostraremos todos los Pokemon y otra donde se mostrarán únicamente los que el usuario eligió.  

Para la primera, donde estarán todos los Pokemon, debemos modificar un poco la forma en la que estamos acostumbrados a crear los templates para las celdas de la lista, y es que necesitamos tomar en cuenta que cada fila de la lista estará ligado a un `SelectableItemWrapper<Pokemon>` y no a un `Pokemon`.  

Declararemos la lista y pondremos la colección `Pokemons`, definida más adelante en el ViewModel, como la fuente de ítems:

```csharp  
var list = new ListView();
list.SetBinding(ListView.ItemsSourceProperty, "Pokemons");
```  

Como sabemos, ahora cada uno de los elementos de la colección será una celda en la lista.

#### PokemonSelectableCell
Para esta celda es necesario que se muestre el nombre, el peso y la altura, además de un control para permitir que dicho pokemon sea seleccionable, es por eso que se crean las propiedades *bindeables* necesarias.  

Es por eso que se crean tres etiquetas para mostrar la información y en este caso se hará uso de `Switch`, que llamaremos `sw`, para marcar la selección de cada uno (intenté usar el <a href="https://github.com/XLabs/Xamarin-Forms-Labs/wiki/Checkbox-Control" target="_blank" rel="nofollow">CheckBox de XLabs</a>, pero encontré un pequeño bug con las listas).  

Tu puedes diseñar tu celda como quieras, pero es importante que dentro de ella *bindees* o ligues una propiedad booleana, como `IsToggled` o `Checked`, a la propiedad `IsSelected` de la clase `SelectableItemWrapper`. Bastará con una línea así:

```csharp  
// Important:
sw.SetBinding(Switch.IsToggledProperty, "IsSelected");
```  

Es importante señalar que dicho *binding* está definido en la implementación de la celda y no en el código de la vista.

### Volviendo a la vista
Ya de vuelta en la vista, podemos hacer uso de la celda en nuestra `ListView`, podemos definir los otros *bindings*:

```csharp  
var template = new DataTemplate(typeof(PokemonSelectableCell));
template.SetBinding(PokemonSelectableCell.NameProperty, "Item.Name");
template.SetBinding(PokemonSelectableCell.WeightProperty, "Item.Weight");
template.SetBinding(PokemonSelectableCell.HeightProperty, "Item.Height");
list.ItemTemplate = template;
```  

Podrás notar que la ruta del *binding* no es "directa", si no que primero nos estamos refiriendo a la propiedad `Item` y luego a las propiedades "reales" y esto es porque estamos *bindeando* un `SelectableItemWrapper<Pokemon>` y no una instancia de `Pokemon`. 

### El ViewModel
Para el ViewModel requeriremos dos colecciones:  
  
 - Una para contener una lista de todos los elementos 
 - Una para contener los elementos seleccionados  
 
 Sus definiciones son las siguientes:
 
```csharp  
private ObservableCollection<SelectableItemWrapper<Pokemon>> _pokemons;
public ObservableCollection<SelectableItemWrapper<Pokemon>> Pokemons
{
    get { return _pokemons; }
    set { _pokemons = value; RaisePropertyChanged(); }
}

private ObservableCollection<Pokemon> _selectedPokemons;
public ObservableCollection<Pokemon> SelectedPokemons
{
    get { return _selectedPokemons; }
    private set { _selectedPokemons = value; RaisePropertyChanged(); }
}
```  

La primera, `Pokemons` es una `ObservableCollection` de `SelectableItemWrapper` de `Pokemon` ya que es la contiene los elementos que serán seleccionables. Mientras que la segunda es una colección como cualquier otra.

Entonces, en el ViewModel podemos obtener todos los elementos seleccionados usando Linq:

```csharp  
ObservableCollection<Pokemon> GetSelectedPokemons()
{
    var selected = Pokemons
        .Where(p => p.IsSelected)
        .Select(p => p.Item)
        .ToList();   
    return new ObservableCollection<Pokemon>(selected);
}
```  

Marcar o desmarcar todos los elementos como seleccionados:

```csharp  
void SelectAll(bool select)
{
    foreach (var p in Pokemons)
    {
        p.IsSelected = select;
    }
}
```  

Y todo, desde el ViewModel. Recuerda, puedes <a href="https://github.com/ThatCSharpGuy/MultiPokeList" target="_blank">ver el código fuente</a>. Y también puedes ver un ejemplo de la app terminada:

<img src="http://i.giphy.com/qpfRFj3MrFqow.gif" title="Multi-select ListView y MVVM" />

#### Créditos
La clase `SelectMultipleBasePage<T>` en la que está completamente basada la información de este post, fue escrita por Glenn Stephens de la  Xamarin University, <a href="https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/multiselect/" target="_blank" rel="nofollow">mira el post original</a>.
