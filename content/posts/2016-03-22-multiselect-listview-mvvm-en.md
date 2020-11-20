---
layout: post
title: Multi-select ListView and MVVM
date: 2016-03-23 08:00:00
author: Antonio Feregrino
summary: Learn how to allow the user to select more than one item in a ListView, everything from MVVM with Xamarin.Forms.
featured_image: multiselect-featured.png
images_folder: /xamarin-forms/
github: https://github.com/ThatCSharpGuy/MultiPokeList
lang: en
tweet_id: 712819064104570880
tags: XamarinForms, Xamarin
featured_tag: XamarinForms
---

Finally I was able to develop something using Xamarin.Forms and part of what I've been doing had to do with implementing a list that allowed the multiple election of rows and was compatible with the MVVM pattern.  

Doing some research, I found that the `ListView` control, which is the most used to show data in a list, does not allow multiple selection and developers should find a workaround to this matter. In Xamarin documentation there is also <a href="https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/multiselect/" target="_blank" rel="nofollow">an article</a> where it is explained a way to complete this task, and this post is based on it. 
  
To make this task possible, we should make use of some auxiliary classes that I will explain later on this post.

### The Model  
For this example, we will be showing a list of Pokemons and allow the user to select some of them through a list, the model class is the following:

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
Apart from the model, there is a need from an auxiliary class that you can tell by the name, acts as wrapper for the model, its definition is the following:

```csharp  
public class SelectableItemWrapper<T>
{
    public bool IsSelected { get; set; }
    public T Item { get; set; }
}
```  

The `IsSelected` property will be used to determine whether the user had selected an item or not, while `Item` will contain the model.

## The View
(Si prefieres trabajar solo con C#, <a href="//thatcsharpguy.com/post/multiselect-listview-mvvm">ve este post</a>)

For this app we will use two Forms pages, one to show all the Pokemons and other to show only those who had been selected by the user. 

For the first one, where all the Pokemons will be shown, we will have to modify slightly the way we are used to create a cell template, and the reason is that every row in the list will be binded to a `SelectableItemWrapper<Pokemon>` not to a `Pokemon`.  

The list will be declared in XAML and the collection `Pokemons` will be set as the `ItemSource` property:

```xml  
<ListView ItemsSource="{Binding Pokemons}">
```  

As we know, now every element in the collection will be a row in the list.

#### PokemonSelectableCell
For this cell it is necessary to show the name, weight and height of the Pokemon binded to it, along with a control to let the user choose or not such item. That is why some bindable properties are needed.

That is why in order to show the information three labels are created, and for the selection issue a `Switch` control will be used (I tried to use the <a href="https://github.com/XLabs/Xamarin-Forms-Labs/wiki/Checkbox-Control" target="_blank" rel="nofollow">CheckBox de XLabs</a>, but I ran into an issue with the `ListView`).  

You can design the cell as you want, but it is important that inside of it you bind a boolean property, such as `IsToggled` o `Checked`, to the property `IsSelected` of the `SelectableItemWrapper` class. For this example, a line like this should be enough:

```csharp  
// Important:
sw.SetBinding(Switch.IsToggledProperty, "IsSelected");
```  

I should remark that the binding is defined within the cell implementation, and not in the view where the bindings are usually defined.

### Back to the View
Now, again back to the view, we can use the cell in our `ListView`, we can define other bindings:

```xml  
<ListView.ItemTemplate>
    <DataTemplate>
        <c:PokemonSelectableCell 
            Name="{Binding Item.Name}" 
            Weight="{Binding Item.Weight}" 
            Height="{Binding Item.Height}" />
```  

You can notice that te binding routes aren't "direct", instead we are referring first to an `Item` before the *real* properties, that is because we are binding to a `SelectableItemWrapper<Pokemon>`, not to a `Pokemon`. 

### The ViewModel
For the ViewModel we require two collections:

 - One to hold a list of all Pokemons 
 - One to hold the selected Pokemons  
 
Here are the definitions for them:
 
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

The first one, `Pokemons` is an `ObservableCollection` of `SelectableItemWrapper` of `Pokemon` because it holds all the selectable items. Whereas the second one is just another collection. Like the ones we are used to.

Therefore, in the ViewModel we can get all the selected items using a simple Linq query:

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

Or mark all or none items as selected:

```csharp  
void SelectAll(bool select)
{
    foreach (var p in Pokemons)
    {
        p.IsSelected = select;
    }
}
```  

And all of that, from the ViewMdoel. Remember, you can always  <a href="https://github.com/ThatCSharpGuy/MultiPokeList" target="_blank">check the source code</a>. By the way, look at the finished app:

<img src="http://i.giphy.com/qpfRFj3MrFqow.gif" title="Multi-select ListView y MVVM" />


#### Credits
The class `SelectMultipleBasePage<T>` in which all the info in this post is based on, was written by Glenn Stephens from Xamarin University, <a href="https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/multiselect/" target="_blank" rel="nofollow">see the original post</a>.