---
layout: post
title: Custom cells en Xamarin.Forms
date: 2017-06-20 19:00:01
author: Antonio Feregrino
summary: Celdas personalizadas para tus aplicaciones de Xamarin.Forms, seguramente ya sabías que podías modificar el comportamiento de tus apps dependiendo de la plataforma... pero probablemente nunca lo habías intentado con una celda de un ListView o TableView
featured_image: featured.jpg
images_folder: /xamarin-forms/customcell/
github: https://github.com/ThatCSharpGuy/CeldaPersonalizada
lang: es
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

En un post anterior les había hablado sobre <a href="..\custom-renderer-paint-code" target="_blank">sobre cómo crear un control personalizado</a> en Xamarin.Forms, pero, ¿sabías que puedes hacer lo mismo con las celdas de un `ListView` o `TableView`? en este post voy a explicar cómo hacerlo. Esta es una solución ideal si tienes conocimiento de creación de interfaces en iOS o Android.  

## En Forms  

Como siempre, cuando vamos a implementar funcionalidades específicas para cada plataforma en Xamarin.Forms, iniciamos con una clase dentro del proyecto de Forms, esta debe ser una abstracción de lo que vamos a implementar. En este caso debe ser una clase que derive de `ViewCell`:  

```csharp  
public class ContactCell : ViewCell
{
```  

Además, dentro de la clase le agregamos una propiedad llamada `ContactName` y su respectiva declaración de `BindableProperty`, tu deberías agregarle las que necesites:

```csharp  
    public static readonly BindableProperty ContactNameProperty =
        BindableProperty.Create(nameof(ContactName), typeof(string), typeof(ContactCell), default(string));

    public string ContactName
    {
        get { return (string)GetValue(ContactNameProperty); }
        set { SetValue(ContactNameProperty, value); }
    }
```  

Por último, sobrescribe el método `OnBindingContextChanged`, este método es llamado cada vez que el objeto enlazado con la celda cambia. Cuando este objeto cambie, nosotros vamos a cambiar el valor de la propiedad. Esto cobrará relevancia más adelante:

```csharp  
    protected override void OnBindingContextChanged()
    {
        string name = BindingContext as string;
        if(name!= null)
            ContactName = name;
    }
```  

## En Android  


### Diseño  

En Android hay que implementar la celda, en esta plataforma la forma por excelencia para crear interfaces gráficas es a través de archivos XML. En este caso, los archivos de interfaz gráfica deben ir en la carpeta `Resources/layout`, yo llamé a la celda `ContactCell.axml`. El contenido es un simple `RelativeLayout` con un par de `TextViews` dentro (asegúrate de establecerle un ID a cada control que vayas a usar):  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="70dp"
    android:padding="0dip">
    <TextView
        android:id="@+id/FirstLetter"
        android:background="@drawable/RoundedCorner"
        android:layout_width="50dp"
        android:layout_height="50dp"
        android:layout_centerVertical="true"
        android:layout_centerInParent="false"
        android:layout_marginLeft="10dp"
        android:textSize="30sp"
        android:textAllCaps="true"
        android:gravity="center" />
    <TextView
        android:id="@+id/FullName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerVertical="true"
        android:layout_centerInParent="false"
        android:textSize="30sp"
        android:layout_marginLeft="10.0dp"
        android:layout_marginRight="10.0dp"
        android:layout_toRightOf="@id/FirstLetter" />
</RelativeLayout>
```  

Inclusive puedes utilizar el editor gráfico de tu preferencia:  

<img src="/images/xamarin-forms__customcell__ContactCell.png" title=""Creación del proyecto"" />

### *Wrapper*

Lo siguiente es crear una clase *wrapper* que envuelva a nuestro archivo XML, esto con la finalidad de poder acceder más fácil a los controles y permitir que nuestra celda funcione con las `CachingStrategy` dentro de las listas de Forms. La clase debe heredar de un contenedor de Android y implementar la interfaz `INativeElementView` de Xamarin.Forms:  

```csharp  
public class AndroidContactCell : LinearLayout, INativeElementView
{
```  

Dentro de esta clase agrega un par de propiedades, la primera para hacer referencia al control de Forms, y la segunda implementando la interfaz `INativeElementView`:

```csharp  
    ContactCell _nativeContactCell;
    public Element Element => _nativeContactCell;
```  

Un par de `TextView` para hacer referencia a lo declarado en el *layout* xml:

```csharp  
    TextView _firstLetterTextView;
    TextView _fullNameTextView;
```  

En el constructor hay que tomar el `Context` y una instancia de la clase de la celda en Forms. Tomando el `Context` vamos a crear una instancia de la celda, empleando el `LayoutInflater`, luego, con esta instancia a la mano, recuperamos los `TextView`.

```csharp  
    public AndroidContactCell(Context context, ContactCell cell) : base(context)
    {
        _nativeContactCell = cell;

        var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.ContactCell, null);
        _firstLetterTextView = view.FindViewById<TextView>(Resource.Id.FirstLetter);
        _fullNameTextView = view.FindViewById<TextView>(Resource.Id.FullName);

        AddView(view);
    }
```  

Por último, agregamos un método que nos permitirá actualizar los valores de esta celda:

```csharp  
    public void UpdateCell(string newName)
    {
        _firstLetterTextView.Text = newName.Substring(0, 1);
        _fullNameTextView.Text = newName;

    }
}
```  

### *Renderer*

Luego, lo que hay que hacer es implementar el *custom renderer* para enlazar la celda de Forms con la nativa que acabamos de desarrollar. El *renderer* debe derivar de `ViewCellRenderer`:

```csharp  
[assembly: ExportRenderer(typeof(ContactCell), typeof(ContactCellRenderer))]
namespace CeldaPersonalizada.Droid.Cells
{
    class ContactCellRenderer : ViewCellRenderer
    {
```  

Dentro del *renderer* agrega una propiedad para contener una instancia de la celda nativa:

```csharp  
        AndroidContactCell _cell;
```  

Después toca sobrescribir un par de métodos, `GetCellCore` y `OnCellPropertyChanged`, el primero que se ejecutará cada vez que el sistema necesite crear una nueva celda. Dentro de éste trataremos de convertir el argumento `convertView` a una instancia de la celda nativa (aquí es donde se realiza el reciclado de celdas) y si no es posible, creamos una nueva. Antes de terminar la ejecución del método, llamamos a `UpdateCell` para establecer los valores correctos:

```csharp  
        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var FormsCell = Cell as ContactCell;
            _cell = convertView as AndroidContactCell;
            if (_cell == null)
            {
                _cell = new AndroidContactCell(context, FormsCell);
            }

            _cell.UpdateCell(FormsCell.ContactName);

            return _cell;
        }
```  

Dentro de `OnCellPropertyChanged` revisamos qué propiedad se modificó y si se modificó la que nos interesa, actualizamos los valores de la celda nuevamente.  

```csharp  
        protected override void OnCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var FormsCell = (ContactCell)sender;
            if (e.PropertyName.Equals("ContactName"))
            {
                _cell.UpdateCell(FormsCell.ContactName);
            }
        }
    }
}
```  

## En iOS  

En iOS también tenemos la opción de crear celdas usando código, o como en este caso, archivos de interfaz gráfica `.xib`, para crear uno en Visual Studio para Mac:

### Diseño

<img src="/images/xamarin-forms__customcell__projectCreation.jpg" title=""Creación del proyecto"" />

Esto generará un par de archivos `ContactCell.cs` y `ContactCell.xib`, es el segundo archivo en el que se edita la interfaz. Yo inclusive lo edité con el editor de Xcode:

<img src="/images/xamarin-forms__customcell__edit.jpg" title=""Creación del proyecto"" />

### *Wrapper*

Una vez que está creada la celda, vamos a echarle un ojo a su clase asociada `ContactCell.cs`, esta clase es la que actuará como el *wrapper* de la interfaz. Entonces, lo que hay que hacer es que implemente la interfaz `INativeElementView`:

```csharp  
public partial class ContactCell : UITableViewCell, INativeElementView
{
```  

Luego, verás unas partes de código que se generaron automáticamente, estas nos serán de gran ayuda cuando sea la hora de crear las celdas.

```csharp  
    public static readonly NSString Key = new NSString("ContactCell");
    public static readonly UINib Nib;

    static ContactCell()
    {
        Nib = UINib.FromName("ContactCell", NSBundle.MainBundle);
    }

    protected ContactCell(IntPtr handle) : base(handle)
    {
        // Note: this .ctor should not contain any initialization logic.
    }
```  

Ya por último, añade una propiedad desde la cual puedas hacer referencia a un tipo `ContactCell` del proyecto de Forms, liego debes agregar una propiedad pública de tipo `Element` para cumplir con la interfaz `INativeElementView` y por último, agrega una forma de actualizar el valor de los campos dentro de la celda:

```csharp  

    public FormsContactCell FormsContactCell { get; set; }

    public Element Element => FormsContactCell;

    public void UpdateCell(string newName)
    {
        FirstLetter.Text = newName.Substring(0, 1);
        FullName.Text = newName;

    }
}
```  

### *Renderer*  

Ya sabes, hay que implementar un *custom renderer*, este igual heredará de `ViewCellRenderer`:

```csharp  
[assembly: ExportRenderer(typeof(FormsContactCell), typeof(CeldaPersonalizada.iOS.Cells.ContactCellRenderer))]
namespace CeldaPersonalizada.iOS.Cells
{
    public class ContactCellRenderer : ViewCellRenderer
    {
```  

Al igual que en el de Android, debes agregar un campo para mantener una referencia a la celda nativa, en este caso se llama `iOSContactCell` porque usé <a href="//thatcsharpguy.com/post/creando-propios-alias/" target="_blank">un alias</a>:

```csharp  
        iOSContactCell _nativeContactCell;
```  

Ya para terminar, hay que sobrescribir el método `GetCell` que es llamado cada vez que una celda necesita ser creada. Lo primero que hay que hacer es tratar de reutilizar la celda que viene en el argumento `reusableCell`. Si no se puede (si es nula), instanciamos una nueva celda a partir del *Nib* que se creó junto con nuestro archivo de interfaz gráfica. Si, por otro lado, se puede reutilizar la celda, tenemos que eliminarle el evento `PropertyChanged` a la celda de Forms, más de esto en corto.

Después, y ya una vez que tenemos una celda nativa que podemos usar, establecemos la propiedad `FormsContactCell` para que haga referencia a la celda de Forms que está vinculada con la celda nativa. A esta misma le agregamos un manejador de evento a `PropertyChanged`, esto con la finalidad de estar atentos a cuando alguna propiedad de nuestra celda de Forms cambia. Por último, actualizamos la celda nativa para que refleje los valores más recientes.

```csharp  
        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            _nativeContactCell = reusableCell as iOSContactCell;
            if (_nativeContactCell == null)
            {
                var stuff = iOSContactCell.Nib.Instantiate(null, null);
                _nativeContactCell = stuff.First() as iOSContactCell;
            } 
            else
            {
                _nativeContactCell.FormsContactCell.PropertyChanged -= FormsContactCell_PropertyChanged;    
            }
            _nativeContactCell.FormsContactCell = item as FormsContactCell;
            _nativeContactCell.FormsContactCell.PropertyChanged += FormsContactCell_PropertyChanged;
            _nativeContactCell.UpdateCell((item as FormsContactCell).ContactName);

            return _nativeContactCell;
        }
```  

El manejador de evento es muy simple. Primero se comprueba que se haya modificado la propiedad que nos interesa, y después actuamos en consecuencia.

```csharp  
        void FormsContactCell_PropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var FormsCell = sender as FormsContactCell;
            if(e.PropertyName.Equals("ContactName"))
            {
                _nativeContactCell.UpdateCell(FormsCell.ContactName);
            }
        }
```  

Eso es todo lo que se requiere hacer para que podamos crear y utilizar celdas personalizadas en Xamarin.Forms, así es como se ve el resultado final:

No olvides que puedes descargar <a href="https://github.com/ThatCSharpGuy/CeldaPersonalizada" target="_blank">el código fuente</a> directamente de <a href="https://github.com/ThatCSharpGuy/CeldaPersonalizada" target="_blank">GitHub</a> para que lo pruebes y modifiques a tu gusto.

