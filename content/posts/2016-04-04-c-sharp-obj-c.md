---
layout: post
title: Xamarin.iOS, C# y Objective-C
date: 2016-04-04 19:00:00
author: Antonio Feregrino
excerpt: Un post en el que se compara la sintaxis de Objective-C con la de C# con Xamarin.iOS.
featured_image: featured_obj.png
images_folder: /xamarin-ios/
github: https://github.com/messier16/Forms.Checkbox
lang: es
tags: Xamarin
featured_tag: Xamarin
---

Cuando conocí <a href="http://thatcsharpguy.com/tag/Xamarin">Xamarin</a> por primera vez, una de mis primeras preguntas era: ¿Cómo demonos se supone que voy a escribir apps para iOS en C#? Simplemente no se me ocurría cómo es que eso era posible... 

Pues bien, hace poco tuve que realizar un proyecto en el que requería mostrar un control tipo *checkbox* para permitir al usuario seleccionar varios items de un conjunto, si estás familiarizado con iOS, sabrás que no existe un *checkbox* nativo, lo más cercano a eso que hay es el `UISwitch`.

Yo no quería usar el UISwitch porque ocupa mucho espacio y creo que da una ilusión como de encendido/apagado más que de seleccionado/no seleccionado, es por eso que *googleando* un poco me encontré con <a href="https://github.com/Marxon13/M13Checkbox" target="_blank" rel="nofollow">M13Checkbox</a> que es un control escrito en Objective-C que hace justo lo que quiero, el problema: **está en Objective-C**.

Y si bien este no es un gran problema, puesto que pude haber <a href="https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/" target="_blank" rel="nofollow">creado un binding</a>, decidí "traducir" la librería de Objective-C a C#, para mostrarte cómo es que en algunos aspectos no hay mucha diferencia entre los dos lenguajes.


## Definición de las clases:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
public class CheckView : UIView
{
    public M13Checkbox Checkbox { get; set; }

    public bool Selected { get; set; }
    
    // Code    
}
  
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
@interface CheckView : UIView
@property (nonatomic, weak) M13Checkbox *checkbox;
@property (nonatomic, assign) BOOL selected;
@end

@implementation CheckView
@synthesize checkbox, selected;
    // Code
@end

```  
</div>  
</div>

En C# no es necesario crear una interfaz para definir una clase, basta con definir la clase incluyéndo sus propiedades, y si bien también existen las interfaces, estas cumplen otras funciones que no son las mismas que Obj-C.

## Creando instancias de objetos

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
checkView = new CheckView(checkViewFrame);
_titleLabel = new UILabel(labelFrame);
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
checkView = [[CheckView alloc] initWithFrame:checkViewFrame];
_titleLabel = [[UILabel alloc] initWithFrame:labelFrame];
```  
</div>  
</div>

La primera diferencia es que nosotros no tenemos que usar `alloc` para reservar memoria para nuestra instancia, ni necesitamos llamar a algún método `init` para inicializarlo. Ambas tareas son reemplazadas por el operador `new` y el constructor de la clase.


## Métodos

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#

// Static method
public static void NoInstanceMethod()
{ // ...

public UIBezierPath GetDefaultShape()
{ // ...
            
public void Draw(CGRect rect)
{ // ...

// Constructor
public M13Checkbox(GCRect frame, string title)
{ // ...
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C

// Class method
+ (void)noInstanceMethod
{ // ...

- (UIBezierPath *)getDefaultShape
{ // ...

- (void)drawRect:(CGRect)rect
{ // ...

// Initializer
- (id)initWithFrame:(CGRect)frame title:(NSString *)title
{ // ...
```  
</div>  
</div>

La primera cosa que resalta a la vista es que en C# desaparecen los `+` y  `-` dentro de la firma del método, sabemos que `+` significa que el método es un método de clase y lo más parecido en C# es un método *static*, no digo que son 100% iguales, porque no lo son, pero para el día a día, son equivalentes.

En Objective-C no existe la sobrecarga de métodos, entonces el nombre del método incluye el nombre del primer parámetro pasado a él como argumento, en este caso "draw**Rect**", para C# esto no es necesario, basta con el nombre del método, los argumentos pasados a él lo diferenciarán de los otros que puedan coincidir en nombre.   

Por otro lado, en C# no existe un método para inicializar un objeto, sino que se ocupan los constructores, que parecen métodos sin tipo de retorno y que llevan el mismo nombre de la clase que construye, como en este caso `M13Checkbox`.

## Llamando a métodos

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
Checkbox.GetDefaultShape().Fill();

fillColor.GetRGBA(out r, out g, out b, out a);

bezierPath.AddCurveToPoint(new CGPoint((0.17625 * height), // ...
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
[[checkbox getDefaultShape] fill];

[fillColor getRed:&r green:&g blue:&b alpha:&a];

[bezierPath addCurveToPoint: CGPointMake((0.17625 * height), // ...
```  
</div>  
</div>

Lo primero que nos damos cuenta es que los `[ ]` desaparecen, en C# los métodos se llaman colocando el operador `.` a nuestro objeto, para después indicar el nombre y su lista de parámetros entre `( )`, y si un método no recibe parámetros los paréntesis deben colocarse vacíos.  

En cuanto a los parámetros, estos no requieren ser distinguidos con el nombre que se indica en la firma del método, basta con colocarlos en el orden correspondiente y separarlos con una coma para distinguirlos uno de otro.

En Obj-C vemos que se usa `&` para indicar que un valor es pasado a un método como referencia, mientras que en C# esto se indica con `out` o `ref`, dependiendo de la situación.

## Propiedades de instancia

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
CGRect boxRect = new CGRect(Checkbox.StrokeWidth, 
    (Frame.Size.Height * Constants.CheckVerticalExtension), 
    (Frame.Size.Height * Constants.BoxSize) - Checkbox.StrokeWidth, 
    (Frame.Size.Height * Constants.BoxSize) - Checkbox.StrokeWidth);
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
CGRect boxRect = CGRectMake(checkbox.strokeWidth, 
    (self.frame.size.height * kCheckVerticalExtension), 
    (self.frame.size.height * kBoxSize) - checkbox.strokeWidth, 
    (self.frame.size.height * kBoxSize) - checkbox.strokeWidth);
```  
</div>  
</div>

La primera diferencia es que no es necesario utilizar `self` para referirse a propiedades de instancia (en C# `this` es la palabra para referirse al objeto desde dentro de él mismo).

La segunda es que en C# no existe la posibilidad de definir valores constantes usando `#define`, para ellas es necesario usar la palabra reservada `const`, y como todo tiene que pertenecer a una clase, yo decidí meterlas en una clase llamada `Constants`.

## Tipos por referencia  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
UIColor fillColor = null;
UIColor strokeColor = null;
UIColor checkColor = null;
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
UIColor *fillColor = nil;
UIColor *strokeColor = nil;
UIColor *checkColor = nil;
```  
</div>  
</div>

Para tener una referencia a `UIColor` no es necesario el sufijo `*`, puesto que en C# UIColor ya es un <a href="http://thatcsharpguy.com/post/tipos-dato-c-sharp/">tipo por referencia</a> y no se usa `*` para hacerlo notar. Ah... otra "gran" diferencia es que `nil` es como  `null`.

## Enums  

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
public enum CheckboxState
{
    Unchecked,
    Checked,
    Mixed
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
typedef NS_ENUM(NSInteger, M13CheckboxState) {
    M13CheckboxStateUnchecked = NO, //Default
    M13CheckboxStateChecked = YES,
    M13CheckboxStateMixed
};
  
```  
</div>  
</div>  

En C# no es posible asociar los valores de un *enum* con booleanos, como en Objective-C, entonces para compararlos es necesario hacer algo como:

<div class="pure-g">
<div class="pure-u-1 pure-u-md-1-2">
```csharp  
// C#
if (Checkbox.CheckboxState == CheckboxState.Unchecked)
{
    fillColor = Checkbox.UncheckedColor;
}
else
{
    fillColor = Checkbox.TintColor;
}
```  
</div>
<div class="pure-u-1 pure-u-md-1-2">
```objectivec  
// Objective-C
if (checkbox.checkState == M13CheckboxStateUnchecked) {
    fillColor = checkbox.uncheckedColor;
} else {
    fillColor = checkbox.tintColor;
}
  
  
  
```  
</div>  
</div>

## Conclusión  
Esta fue un pequeño ejercicio de comparación de sintaxis entre los dos lenguajes, en este podemos ver que, si bien hay diferencias muy relevantes, también hay puntos en los que se cruzan o no es mucha la diferencia. También aproveché para mostrar el gran esfuerzo que hizo la gente de Xamarin al mapear 1:1 la api de iOS a C#, es por eso que los nombres de los métodos, clases y demás coinciden casi al 100%.

### Lo que sigue
Si quieres seguir leyendo más sobre la transición de Objective-C a C#, puedes ver <a href="https://developer.xamarin.com/guides/ios/advanced_topics/xamarin_for_objc/primer/" target="_blank" rel="nofollow">documentación de Xamarin</a> o <a href="http://stackoverflow.com/a/1369221" target="_blank" rel="nofollow">esta respuesta en StackOverflow</a>. Yo pronto estaré publicando una pequeña guía sobre cómo hacer una app nativa para iPhone usando C# y Xamarin, para continuar con este ejemplo, pero por mientras puedes ver las <a href="http://thatcsharpguy.com/post/xamarin-android-vs-traditional/">diferencias entre Xamarin.Android y Android</a>.