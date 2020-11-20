---
layout: post
title: MVVM en Xamarin.Forms, caso práctico
date: 2016-06-26 21:00:00
author: Antonio Feregrino
categories: c-sharp
excerpt: Hice una pequeña app con MVVM y en este post te explico algunas de sus partes escenciales de ella, ah... ¡la app es de Pokémon!
images_folder: /mvvm/
featured_image: mvvm-featured.png
lang: es
github: https://github.com/ThatCSharpGuy/xfmvvm
tags: Xamarin, XamarinForms, Mvvm
featured_tag: XamarinForms
---

Mvvmdex
=  
Soy un gran fanático de Pokémon y es por eso que para este post haremos una aplicación que se encargará de buscar pokémons en la PokéAPI usando la <a href="#">Jirapi</a> y mostrar los datos encontrados en la pantalla. En esta aplicación se implementa el patrón MVVM para la separación de responsabilidades.

<img src="http://i.giphy.com/Mo4P4sEJNUKeQ.gif" />

## Introducción  
Dentro de este post voy a asumir que ya tienes un entendimiento básico de cómo es que funciona Xamarin, Xamarin.Forms y la teoría de MVVM, si no, te invito a que consultes mi post sobre [qué es Xamarin](../../tv/xamarin/), el [video sobre Xamarin.Forms](../../tv/xamarin-forms/), y el [video sobre MVVM](../../tv/mvvm).

## Estructura

Como ya sabrás, el <a href="organizacion-codigo-visual-studio">código en Visual Studio</a> se organiza en proyectos, y esta es la organización de los proyectos para esta pequeña app:

<img src="/images/mvvm__codeorg.png" title=""Code organization"" />

La separación de MVVM se puede observar en que el proyecto `Mvvmdex.Views` se relaciona con `Mvvmdex.ViewModels` y a su vez este último se relaciona con `Mvvmdex.Models`, **nunca hay relacion directa entre las vistas y los modelos**.

## Modelo  

Para este proyecto, el modelo está contenido dentro de `Mvvmdex.Models`. Como sabe, en el modelo es donde se realiza el acceso a datos y la lógica de la aplicación. En esta aplicación, el *Mvvmdex*, únicamente se consulta a la PokéAPI dentro de la clase `MvvmdexClient`. Como puedes ver, no tiene ninguna relación con la vista:

```csharp  
public async Task<Pokemon> SearchForPokemon(string pokemonName)
{
	try
	{
		var pkmn = await _client.Get<Jirapi.Resources.Pokemon>(pokemonName);
		var species = await pkmn.Species.GetResource(_client);
		return new Pokemon
		{
			Id = pkmn.Id,
			Name = pkmn.Name,
			Description = species.FlavorTextEntries
				.FirstOrDefault(te => te.Language.Name == "en")?
				.FlavorText.Replace("\n",""),
			Shape = species.Shape.Name
		};
	}
	catch
	{
		return null;
	}
}
```  

## ViewModels

Para este proyecto, el modelo está contenido dentro de `Mvvmdex.ViewModels`. Esta es la parte más complicada, hay que recordar que este es el intermediario entre la vista y el modelo. Para esta app únicamente existe un solo ViewModel, `PokemonSearchViewModel`, pero es muy común que tu tengas varios dependiendo del tamaño y complejidad de tu aplicación. 

Esta sección tiene relación directa con el Modelo, en el *Mvvmdex* se hace referencia en el cliente `MvvmdexClient`:

```csharp  
private MvvmdexClient _client;

public PokemonSearchViewModel()
{
	_client = new MvvmdexClient(); // <- Model
}
```  

El modelo puede enviar y recibir mensajes del *viewmodel* a través de métodos.

### INotifyPropertyChanged

La interfaz `INotifyPropertyChanged` permitirá a la vista ser notificada cada vez que suceda algún cambio en el *viewmodel*. La interfaz únicamente expone el evento `PropertyChanged` que debemos invocar cada vez que queremos notificar a la vista algún cambio. Para hacer la tarea más sencilla, se crea un método auxiliar:

```csharp  
public void RaiseOnPropertyChange([CallerMemberName] string propertyName = null)
{
	if (PropertyChanged != null)
	{
		PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
	}
}
```  

El atributo `CallerMemberName` nos facilitará la tarea, ya que con él aseguramos que el nombre de la propiedad desde la que lo llamemos será colocada ahí para notificar a la vista sobre la propiedad correcta.  

Es importante decir que existen muchos frameworks de *mvvm* que ya contienen una implementación de esta interfaz y para nosotros bastaría con derivar nuestros objetos de dicha implementación para no tener que hacerla nosotros mismos.

### Propiedades  

Con las propiedades hay una pequeña limitante: no pueden ser propiedades auto-implementadas, ya que es necesario llamar a nuestro método auxiliar creado más arriba. Mira la propiedad `PokemonName`:

```csharp  
private string _pokemonName;

public string PokemonName
{
	get { return _pokemonName; }
	set { _pokemonName = value; RaiseOnPropertyChange(); /* RaiseOnPropertyChange("PokemonName") */ }
}
```  

Como puedes ver, cada vez que ocurre un cambio en la propiedad, se está notificando a quién desee sobre el cambio, en nuestro caso, la vista es quien desea ser notificada. Más adelante, con los *data bindings*, enlazaremos las propiedades en el *viewmodel* con elementos dentro de la vista.
 
### Commands  

Los *commands* es otro de los mecanismos que contempla *mvvm* para la comunicación entre los componentes, y es a través de ellos que se trasladan algunos de los eventos generados en la vista hacia el *viewmodel*. En Xamarin.Forms un comando no es más que una instancia de objeto que implementa la interfaz `ICommand`, en el caso de esta app, tenemos el comando `BuscaPokemonCommand` que lo único que hace es ejecutar una `Action` cuando se ejecuta.

```csharp  
public class BuscaPokemonCommand : ICommand
{
	private readonly Action _search;
	public BuscaPokemonCommand(Action search)
	{
		_search = search;
	}

	public bool CanExecute(object parameter)
	{
		return true;
	}

	public void Execute(object parameter)
	{
			_search();
	}

	public event EventHandler CanExecuteChanged;
}
```  

La interfaz ICommand contiene tres miembros:  
  
 - `bool CanExecute(object parameter)` - con el cual podemos decidir si el comando se puede ejecutar 
 - `void Execute(object parameter)` - en el cual debemos efectuar la ejecución
 - `event EventHandler CanExecuteChanged` - es un evento que debe invocarse cada vez que las condiciones bajo las que se puede invocar un comando han cambiado.

Es importante mencionar que al igual que con la interfaz `INotifyPropertyChanged`, ya existen varios frameworks que proveen implementaciones genéricas de `ICommand` para evitarnos el tedio de escribirlos nosotros mismos.

#### En el ViewModel

Ahora, la forma en la que se usa este comando es a través de una propiedad ya que más adelante será enlazada con un control dentro de la vista:

```csharp  
private ICommand _buscaPokemonCommand;
public ICommand BuscaPokemonCommand
{
	get
	{
		if (_buscaPokemonCommand == null)
		{
			Action buscaPokemonAction = async () =>
			{
			var pokemon = await _client.SearchForPokemon(SearchTerms.ToLower());

				HasCoincidence = pokemon != null;
				if (HasCoincidence)
				{
					Description = pokemon.Description;
					PokemonName = String.Format("{0:D3} {1}", pokemon.Id, pokemon.Name);
					Shape = pokemon.Shape;
				}
			};
			_buscaPokemonCommand = new BuscaPokemonCommand(buscaPokemonAction);
		}
		return _buscaPokemonCommand;
	}
}
```  

## Vista
 
Hay que recordar que en Forms podemos crear nuestras interfaces a través de código C# o XAML, para esta ocasión usaré XAML para crear la pantalla.

La vista de la aplicación es bastante simple, únicamente consta de un cuadro de búsqueda (`SearchBar`), un contenedor (`StackLayout`) que contiene varias etiquetas (`Label`) para mostrar los datos de Pokémon en cuestión y por último una etiqueta para mostrar en caso de que no encontremos un pokémon que coincida con nuestra búsqueda:

```xml  
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" 
		xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
		xmlns:converters="clr-namespace:Mvvmdex.Views.Converters" 
		x:Class="Mvvmdex.Views.MvvmdexPage" 
		Title="Mvvmdex">
	<ContentPage.Resources>
		<ResourceDictionary>
			<converters:BooleanInverterConverter x:Key="BooleanInverter" />
			<converters:ShapeToEmojiConverter x:Key="ShapeToEmoji" />
		</ResourceDictionary>
	</ContentPage.Resources>
	<Grid>
		<Grid.RowDefinitions>
			<RowDefinition Height="Auto" />
			<RowDefinition Height="*" />
		</Grid.RowDefinitions>
		
		<SearchBar Grid.Row="0" SearchCommand="{Binding BuscaPokemonCommand}" 
			Text="{Binding SearchTerms}" />
		
		<StackLayout IsVisible="{Binding HasCoincidence}" Grid.Row="1" Padding="20" Spacing="10">
			<Label Text="{Binding PokemonName}" FontSize="Large" />
			<Label Text="{Binding Description}" />
			<Label Text="{Binding Shape, Converter={StaticResource ShapeToEmoji}}" />
		</StackLayout>
		
		<Label IsVisible="{Binding HasCoincidence, Converter={StaticResource BooleanInverter}}" 
			Text="😕" Grid.Row="1" HorizontalTextAlignment="Center" FontSize="100" />
	</Grid>
</ContentPage>
```  

### Data bindings

Si ves mucho `Binding` en el código no te preocupes, es algo muy común en *MVVM*, y es que es ahí donde ocurre el enlace de la vista al *viewmodel*, una de las partes centrales del patrón. Los *data bindings* se encargan de "estar al tanto" de los cambios que informa el *viewmodel* y reflejarlos en la pantalla cuando sucedan.

Por ejemplo, la etiqueta en la que se muestra el nombre y el número del Pokémon:

```xml  
<Label Text="{Binding PokemonName}" FontSize="Large" />
```  

Mediante los *bindings* el texto del `Label` cambiará cada vez que la propiedad `PokemonName` lo haga.

Sin embargo, los bindings no son solo de una dirección (*viewmodel* → vista), sino que también pueden ser usados al revés. Tomemos, por ejemplo el control `SearchBar`:

```xml  
<SearchBar Grid.Row="0" SearchCommand="{Binding BuscaPokemonCommand}" Text="{Binding SearchTerms}" />
```  

Entonces cada vez que el usuario cambie el texto de la caja de búsqueda, la propiedad `SearchTerms` del *viewmodel* también cambiará. Y no solo eso, sino que también el control tiene enlazado el comando `BuscaPokemonCommand`, el comando se ejecutará cuando el usuario decida buscar Pokémons.  

Como puedes ver, las tres propiedades (`PokemonName`, `SearchTerms y `SearchCommand`) existen en el *viewmodel*. En caso de que no existiera alguna, no pasará absolutamente nada, no habrá errores ni excepciones, es por eso que se dice que es un poco difícil de *debuggear* estos enlaces de datos.  

### Converters  

Además de `Binding` probablemente te hayas fijado en la palabra `Converter`... así que es hora de explicar los *converters*. Estos no son más que instancias de tipos que implementan la interfaz `IConvertValue` que en pocas palabras hace eso: convertir valores.

En el más estricto de los sentidos el *viewmodel* debe ser independiente de la plataforma y exponer solamente las propiedades necesarias para que la vista opere. Pero, ¿qué pasa si nosotros queremos extender un poco más esa funcionalidad? 

Por ejemplo, en la app *Mvvmdex* quisiera mostrar u ocultar un panel dependiendo de si el Pokémon fue encontrado o no, el *viewmodel* ofrece la propiedad booleana `HasConicidence` que podemos ligar a `IsVisible`:

```xml  
<StackLayout IsVisible="{Binding HasCoincidence}" ... />
```  

Sin embargo, no podemos ligar esa propiedad directamente con otro control para que se "esconda" cuando haya coincidencia, es por eso que se implementó la clase `BooleanInverterConverter`:

```csharp  
public class BooleanInverterConverter : IValueConverter
{
	public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
	{
		return !(bool)value;
	}

	public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
	{
		throw new NotImplementedException();
	}
}
```  

Que como puedes ver tiene dos métodos, uno para convertir "de ida" y uno "de vuelta", esta app solo hace uso del "de ida" y únicamente niega el valor booleano que se le pase.

Luego entonces ya podemos usarlo en nuestra pantalla, primero declarándolo dentro de los recursos de la pantalla:

```xml  
<ContentPage.Resources>
	<ResourceDictionary>
		<converters:BooleanInverterConverter x:Key="BooleanInverter" /> 
```  

Para luego usarlo junto con un enlace a datos en un control:

```xml  
<Label IsVisible="{Binding HasCoincidence, Converter={StaticResource BooleanInverter}}" 
	Text="😕" Grid.Row="1" HorizontalTextAlignment="Center" FontSize="100" />
```  

### Enlace con el ViewModel

Para terminar todo esto, falta un paso muy importante, y es el de relacionar de alguna manera la vista con el *viewmodel*. Hay muchas maneras de hacer esto, sin embargo, una de las más prácticas es establecer el nuestro *viewmodel* como el `BindingContext` de la vista. Para esta app, la acción se realiza en el *code behind* de la página `MvvmdexPage`:

```csharp  
public partial class MvvmdexPage : ContentPage
{
	public MvvmdexPage()
	{
		BindingContext = new PokemonSearchViewModel(); // <- ViewModel
		// ....
```  

## Para cerrar

Existen muchas más posibilidades para hacer más robusta una aplicación de Forms con *MVVM*, como el uso de la inyección de dependencias para integrar más capacidades como navegación entre pantallas, acceso a sensores del teléfono y más. También puedes integrar frameworks como <a href="http://www.mvvmlight.net/doc/" target="_blank" rel="nofollow">mvvm light</a> o <a href="https://github.com/PrismLibrary/Prism#prism" target="_blank" rel="nofollow">Prism</a> para liberarte de hacer tus propias implementaciones de `ICommand` e `INotifyPropertyChanged`. 

Espero que este post te haya servido, recuerda que la mejor forma de aprender es experimentando, así que te invito a que <a href="https://github.com/ThatCSharpGuy/xfmvvm" target="_blank" rel="nofollow">descargues el código de GitHub</a> y juegues un poco con él, si tienes dudas, puedes contactarme sin problemas.