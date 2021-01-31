---
layout: post
title: Xamarin.Forms y PaintCode
date: 2017-06-06 19:00:01
author: Antonio Feregrino
summary: Lleva tus dibujos vectoriales a tu app móvil usando PaintCode y los custom renderers de Xamarin.Forms
featured_image: featured.png
images_folder: /xamarin-forms/paintcode/
github: https://github.com/ThatCSharpGuy/sharp-paintcode
lang: es
tags: Xamarin, XamarinForms
featured_tag: XamarinForms
---

La semana pasada <a href="https://www.paintcodeapp.com/" target="_blank">PaintCode 3</a> estuvo de descuento, así que me dio una buena idea para escribir. En este post voy a explicar cómo crear un control personalizado usando PaintCode y los *custom renderers* de Xamarin.Forms, comenzando desde el dibujo mismo, tal vez sea un poco largo, pero estoy seguro que puede valer la pena.  

## El dibujo en paint code  
Si ya has creado y exportado gráficos usando PaintCode probablemente te quieras saltar esta sección. Si no, te digo que apenas lo abras, debes añadir un nuevo canvas, en mi caso lo llamé *SharpCanvas* y lo hice de 100 por 100, o una relación de aspecto de 1:1, que es la misma que el dibujo que voy a crear:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__1sharpCanvas.png" title="SharpCanvas en blanco" />

Después, añade un *Frame*, que yo llamaré *SharpFrame*:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__2frame.png" title="SharpFrame en blanco" />

Luego, en la parte izquierda de PaintCode hay que agregar un color llamado *FillColor*, y la estableceremos como *Parameter*:

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__3fillColor.png" title="SharpFrame en blanco" />

También hay que agregar un par de variables: *Width* y *Height* que valgan lo que mide nuestro *frame*, también como parámetros:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__4variables.png" title="Agregar variables" />

Después, con el *frame* seleccionado, vamos a arrastrar desde el panel de variables hasta las propiedades del *frame*, de tal modo que la variable *Width* quede ligada con la propiedad *Width* del *frame*, haremos lo mismo para *height*:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__5setProperties.png" title="Vincular variables" />

Y ahora si, podemos hacer nuestro dibujo:  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__6drawing.png" title="Dibujo" />

Lo siguiente es ligar el color que creamos antes (*FillColor*) con el relleno del dibujo recién creado, si lo hicimos de forma correcta, el color de nuestro dibujo cambiará al de nuestro *FillColor*:   

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__7linkColor.png" title="Dibujo" />

Ahora, el siguiente paso es muy, muy importante si queremos que el dibujo hecho se ajuste si el *frame* cambia de tamaño. Si el dibujo que hiciste es una curva béizer (como es mi caso), da doble click sobre él, hasta que en su trazo aparezcan un grupo de puntos:   

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__8waypoints.png" title="Waypoints" />

Una vez que aparezcan, selecciónalos y en el panel de la derecha, justo debajo del menú *Transforms* da click sobre las "I" en el recuadro, de tal forma que las cuatro líneas que unen al punto central con el cuadrado sean líneas zigzagueantes:   

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__9selectWaypoints.png" title="Select waypoints" />

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__10shrinkBehavior.png" title="Zigzag" />

Podrás verificar que todo ha salido bien si al cambiar el valor de las variables *Height* y *Width* en el panel izquierdo, tu dibujo cambia de tamaño.

Para terminar por el momento con PaintCode, de entre las pestañas superiores, selecciona la que dice *"StyleKit"* y luego, del lado derecho cambia las propiedades a tu gusto para que coincida con tu proyecto, en este caso yo las dejé así:     

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__11styleKit.png" title="Zigzag" />

## El código en Xamarin.Forms

Lo primero que vamos a hacer en el código, es crear una clase que servirá para hacer referencia a nuestro control. En mi caso la llamé `SharpView`, sin importar cómo la llames, debes hacer que derive de `View`:  

```csharp  
public class SharpView : View
{
}
```  

A ella hay que agregarle todas las propiedades que deseemos, en este caso le agregaré una sola (`FillColor`) para hacer referencia al relleno de nuestro dibujo:

```csharp  
    public static readonly BindableProperty FillColorProperty =
        BindableProperty.Create(nameof(FillColor), typeof(Color), typeof(SharpView), default(Color));

    public Color FillColor
    {
        get { return (Color)GetValue(FillColorProperty); }
        set { SetValue(FillColorProperty, value); }
    }
```  

Por último, estableceremos unas cuantas propiedades por default, estas mejorarán la apariencia del control y siempre podrán ser sobrescritas si así lo deseamos:

```csharp  
    public SharpView()
    {
        BackgroundColor = Color.Transparent;
        HorizontalOptions = LayoutOptions.Center;
    }
```  

Y habremos terminado con el proyecto de Forms.

## El código en Xamarin.iOS  

En el proyecto de iOS vamos a crear dos nuevas clases: el control "nativo" y el *custom renderer*:

### Control nativo

Comenzando con el control nativo, hay que crear una clase que herede de `UIView`, yo la llamé `UISharpView`:  

```csharp  
public class UISharpView : UIView
{
}
```  

A la clase hay que agregarle al menos un constructor que reciba un `CGRect` como argumento, esto con la finalidad de enviarlo a la clase padre y que nuestro control tome sus dimensiones de ahí:

```csharp  
    public UISharpView(CGRect rect) 
        : base(rect) { }
```  

También hay que agregarle una propiedad que se corresponda con la propiedad `FillColor` del control en Forms, el nombre puede ser cualquiera, pero por consistencia, yo le llamé también `FillColor`. Si te das cuenta, dentro de la propiedad se llama al método `SetNeedsDisplay` que hará que cada vez que el valor cambie, el sistema redibuje el control:

```csharp  
    UIColor _fillColor = UIColor.FromRGB(60, 138, 63);

    public UIColor FillColor
    {
        get { return _fillColor; }
        set { _fillColor = value; SetNeedsDisplay(); }
    }
```  

Ahora vamos a volver por un momento a PaintCode para recuperar el código del dibujo. En el panel de exportación de código asegúrate de que esté seleccionado "iOS > C# Xamarin"  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__12codeSelection.png" title=""Selección de código C# Xamarin"" />

Y copia todo hacia la clase que acabas de crear, tal vez tengas que agregar un par de referencias para que compile. Ya por último, sobrescribe el método `Draw` y dentro de él llama al método que acabas de copiar (en mi caso se llama `DrawSharpCanvas` por el nombre que le puse al canvas al inicio de este post), pasándole el color de relleno y las dimensiones del *frame*:

```csharp  
    public override void Draw(CGRect rect)
    {
        DrawSharpCanvas(FillColor, rect.Width, rect.Height);
    }
```  

### *Custom renderer*

Ahora toca el turno de implementar el *custom renderer*. Lo primero que hay que hacer es agregar una nueva clase que herede de `ViewRenderer<TAbstraction, TNative>`, para no confundirnos tanto, la llamaré `SharpViewRenderer` y tendrá como `TAbstraction` a la clase de Forms y como `TNative` a la clase `UISharpView`, tampoco olvides ponerle el atributo `ExportRenderer`:  

```csharp  
[assembly: ExportRenderer(typeof(SharpView), typeof(SharpViewRenderer))]
namespace SharpPaintCode.iOS.Controls
{
    public class SharpViewRenderer : ViewRenderer<SharpView, UISharpView>
    {
```  

Lo siguiente es crear dentro de ella una instancia de `UISharpView` y establecerla como el elemento que debe mostrarse en pantalla, esto se hace sobrescribiendo el método `OnElementChanged`. Tomaremos las dimensiones de las propiedades `WidthRequest` y `HeightRequest` para su tamaño, así mismo usaremos la propiedad `FillColor` para asignarle un color desde el inicio:

```csharp  
    protected override void OnElementChanged(ElementChangedEventArgs<SharpView> e)
    {
        base.OnElementChanged(e);

        if (e.NewElement != null)
        {
            if (Control == null)
            {
                var sharpFrame = new CGRect(0, 0, Element.WidthRequest, Element.HeightRequest);
                var native = new UISharpView(sharpFrame);
                native.FillColor = Element.FillColor.ToUIColor();
                SetNativeControl(native);
            }
        }
    }
```  

Por último, necesitamos una forma de hacer que el color del control cambie si desde Forms nosotros cambiamos la propiedad `FillColor`, por eso, dentro del *renderer* sobrescribiremos el método `OnElementPropertyChanged` y cambiaremos el color del control nativo siempre y cuando la propiedad que deseamos haya sido modificada:

```csharp  
    protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs e)
    {
        if(e.PropertyName.Equals(nameof(SharpView.FillColor)))
            Control.FillColor = Element.FillColor.ToUIColor();
        else
            base.OnElementPropertyChanged(sender, e);
    }
```  

Y eso será todo en el proyecto de iOS.

## El código en Xamarin.Android  

De igual manera, en este proyecto tendremos que crear una vista nativa y un *renderer*, comenzaremos por la vista nativa:

### Control nativo  

En Android crearemos la clase de nuestro control, siguiendo las buenas prácticas del desarrollo para la plataforma, la llamaremos `SharpView`, en este caso también heredará de `View`, solo que debes tener encuenta de que derive de `Android.Views.View` y no de `Xamarin.Forms.View`:

```csharp  
public class SharpView : View
{
}
```  

Esta clase debe tener al menos un constructor que reciba una instancia de `Context`, para pasarlo a la clase base:  

```csharp  
    public SharpView(Context context) : base(context)
    {
    }
```  

Ahora, también hay que agregar una nueva propiedad, `FillColor` para establecer el color de relleno del dibujo, nota como dentro de esta propiedad se llama al método `Invalidate` que hará que el control se redibuje cada vez que se establezca un nuevo color:  

```csharp  
    public Color _fillColor = Color.Argb(255, 60, 138, 63);
    public Color FillColor
    {
        get { return _fillColor; }
        set
        {
            _fillColor = value;
            Invalidate();
        }
    }
```  

Ahora, atención: PaintCode aún no tiene soporte para Xamarin.Android, pero en realidad no importa mucho, puesto que la diferencia entre Java y C# con Xamarin no es mucha. Para comenzar, en PaintCode asegúrate de seleccionar "Android > Java":  

<img src="https://thatcsharpguy.github.io/postimages/xamarin-forms__paintcode__13androidJava.png" title=""Selección de código C# Xamarin"" />

Ahora, en tu proyecto de Android crea una nueva clase llamada `SharpKit`, borra el contenido luego copia en ella todo el código que te aparece en el panel de PaintCode una vez que seleccionaste "Android > Java"... un montón de errores, ¿cierto? Ahora primero deshazte de la declaración `package` y de todos los `import`, si quieres, mete en un *namespace* la clase que queda.

Ahora, añade los siguientes `using` hasta arriba del archivo:

<pre>
using System;
using Android.Graphics;
</pre>

Lo siguiente es convertir *todos* los métodos, en Java es convención que los métodos y propiedades comiencen con minúscula, así que para este caso bastará con convertir a mayúscula todos, puedes buscar y reemplazar `.save(` por `.Save(`, `.set(` por `.Set(`, `.reset(` por `.Reset(`, `.moveTo(` por `.MoveTo(`, `.cubicTo(` por `.CubicTo(`, `.width(` por `.Width(`, `.height(` por `.Height(`, `.top` por `.Top`, `.left` por `.Left` y así funciona para la mayoría. 

Quizás las conversiones más difíciles serán loas relacionadas con `enum`, toma en cuenta la siguiente tabla por si te resulta de utilidad:

<pre>
paint.setFlags(Paint.ANTI_ALIAS_FLAG);      →   paint.Flags = PaintFlags.AntiAlias;
paint.setStyle(Paint.Style.FILL);           →   paint.SetStyle(Paint.Style.Fill);
paint.setColor(fillColor);                  →   paint.Color = new Color(fillColor);
canvas.drawPath(sharpSymbolPath, paint);    →   canvas.DrawPath(sharpSymbolPath, paint);
</pre>

Una vez que ya compila todo nuevamente, dentro de la clase de tu control nativo debemos sobrescribir el método `OnDraw` para que utilice el método `DrawSharpCanvas` de la clase que acabamos de agregar. Recuerda que hay que pasarle el color y las dimensiones del control:  

```csharp  
    protected override void OnDraw(Canvas canvas)
    {
        SharpKit.DrawSharpCanvas(canvas, _fillColor.ToArgb(), Width, Height);
    }
```  

Y listo, ahora hay que agregar el *custom renderer*

### *Custom renderer*  

Nuevamente, dentro del proyecto de Android  hay que agregar una nueva clase que herede de `ViewRenderer<TAbstraction, TNative>`, para no confundirnos tanto, la llamaré `SharpViewRenderer` y tendrá como `TAbstraction` a la clase `SharpView` (del proyecto central) y como `TNative` a la clase `SharpView` (del proyecto de Android). En este caso haré uso de un par de alias para que no se confundan las clases. Tampoco olvides ponerle el atributo `ExportRenderer`:  

```csharp  
using FormsSharpView = SharpPaintCode.Controls.SharpView;
using NativeSharpView = SharpPaintCode.Droid.Controls.Native.SharpView;

[assembly: ExportRenderer(typeof(FormsSharpView), typeof(SharpViewRenderer))]
namespace SharpPaintCode.Droid.Controls
{
    public class SharpViewRenderer : ViewRenderer<FormsSharpView, NativeSharpView>
    {
```  

Lo siguiente es crear una instancia de `NativeSharpView` y establecerla como el elemento que debe mostrarse en pantalla, para lograrlo, sobreescribe el método `OnElementChanged`, el control nativo requiere pasarle la propiedad `Context` del *renderer* y usaremos la propiedad `FillColor` para asignarle un color desde el inicio:

```csharp  
    protected override void OnElementChanged(ElementChangedEventArgs<FormsSharpView> e)
    {
        base.OnElementChanged(e);

        if (e.NewElement != null)
        {
            if (Control == null)
            {
                var native = new NativeSharpView(Context);
                native.FillColor = Element.FillColor.ToAndroid();
                SetNativeControl(native);
            }
        }
    }
```  

También necesitamos una forma de hacer que el color del control cambie si desde Forms nosotros cambiamos la propiedad `FillColor`, por eso, dentro del renderer sobrescribiremos el método `OnElementPropertyChanged` y cambiaremos el color del control nativo siempre y cuando la propiedad que deseamos haya sido modificada:

```csharp  
    protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs e)
    {
        if (e.PropertyName.Equals(nameof(FormsSharpView.FillColor)))
        {
            Control.FillColor = Element.FillColor.ToAndroid();
        }
        else
        {
            base.OnElementPropertyChanged(sender, e);
        }
    }
```  

Y eso será todo en el proyecto de Android.  

## Demo  
<strong><a href="https://github.com/ThatCSharpGuy/sharp-paintcode" target="_blank">Recuerda que puedes descargar una versión completa de este proyecto</a></strong> en GitHub para que juegues y aprendas con él.

Para probar el control cree una pequeña app que permite cambiar el color de dos `SharpViews`, se ve así en iOS (luce igual en Android):  

<img src="https://media.giphy.com/media/3oKIPrccIwjWhlrBNS/giphy.gif" />

Y con un poco más de imaginación y paciencia, puedes hacer controles aún más interactivos:

<img src="https://media.giphy.com/media/3oKIPifbi3CJj1g2Dm/giphy.gif" />

