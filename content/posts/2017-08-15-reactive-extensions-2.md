---
layout: post
title: Programación reactiva y Rx, parte 2
date: 2017-08-15 19:00:01
author: Antonio Feregrino
excerpt: La programación reactiva es una de las formas de implementar el patrón del observador en nuestro código, y las Reactive Extensions hacen esta tarea mucho más sencilla.
featured_image: fishing.png
images_folder: /rx/
github: https://github.com/ThatCSharpGuy/rx
lang: es
tags: Rx, ReactiveExtensions
featured_tag: Rx
---

<a href="..\reactive-extensions-1" target="_blank">En el post pasado</a> les comencé a hablar de las *Reactive Extensions* y cómo es que introducían un paradigma de programación distinto. Ahí mostré un ejemplo muy sencillo de la programación reactiva... pero sé que no siempre todo el código que escribiremos será así de sencillo, así que en este post crearé y mostraré un caso más complejo en el que podemos utilizar *Rx*.

## El río  
Piensa en un río en el cual hay un conjunto de peces que viajan a través de él, es por eso que los pescadores se acercan al río para tratar de conseguir un poco de alimento. El río produce una cantidad `x` de peces que pasan por él uno a uno cada `y` milisegundos, además de que hay ocasiones en que el agua del río hace muy peligroso pescar en él.  

### `River`

Para recrear las condiciones anteriores, crea una clase llamada `River` que reciba y almacene las especificaciones de la cantidad de peces, el tiempo entre ellos y si es o no peligroso:

```csharp  
public class River
{
    private readonly int _fishAmount;
    private readonly int _waitTime;
    private readonly bool _dangerousRiver;


    public River(int fishAmount, int waitTime = 500,  bool dangerousRiver = false)
    {
        _fishAmount = fishAmount;
        _waitTime = waitTime;
        _dangerousRiver = dangerousRiver;
    }
```  

### `Fish`

Pero aguarda, no te olvides de los peces: estos tendrán una especie (`FishSpecies`), un color (`Color`) y un peso (`Weight`), todo representado dentro de una clase: 

```csharp  
    public class Fish
    {
        public double Weight { get; set; }
        public Color Color { get; set; }
        public FishSpecies Species { get; set; }

        #region Random fish

        private static readonly Random R = new Random();
        private const double MinWeight = 500; // Gramos
        private const double MaxWeight = 5000; // Gramos

        public static Fish RandomFish()
        {
            var fish = new Fish
            {
                Weight = (R.NextDouble() * (MaxWeight - MinWeight)) + MinWeight,
                Species = (FishSpecies) R.Next(4), // There are four species
                Color = (Color) R.Next(4) // There are four colors
            };

            return fish;
        }

        #endregion
    }
```  

Oh, además en esta clase introduje un método para crear peces aleatoriamente.

### `River` (cont)

Bueno, volviendo al diseño del río. Ahora toca escribir la forma en que representaremos el paso de los peces a través de él. ¿Estás de acuerdo en que es un **flujo**? sí, un flujo de peces.  Y al ser un flujo es el un buen candidato para convertirse en un `IObservable`de `Fish`.

Para acceder al flujo de peces usaremos un método que retorne un `IOBservable<Fish>`. Acá es una buena oportunidad para decir que no es recomendable que tu mismo implementes la interfaz, lo mejor en muchos casos es hacer uso de las *Reactive Extensions* para crear un objeto de ese tipo:

```csharp  
    public IObservable<Fish> Stream()
    {
        return Observable.Create<Fish>(observer =>
        {
            for (int i = 0; i < _fishAmount; i++)
            {
                var fish = Fish.RandomFish();
                observer.OnNext(fish);
                Thread.Sleep(_waitTime);

                if (_dangerousRiver && i == _fishAmount / 3)
                    throw new Exception("Uh, hubo un derrame en el río");
            }
            observer.OnCompleted();
            return () => { };
        });
    }
```  

Vamos a revisar qué es lo que está ocurriendo en ese método:  

<dl>
   <dt>1.</dt>
    <dd>Lo primero que nos damos cuenta es el tipo de retorno: <code>IObservable&lt;Fish&gt;</code></dd>
   <dt>2.</dt>
    <dd>Inmediatamente después tenemos la creación de nuestro observable mediante el método estático <code>Observable.Create</code>, este método recibe un `Func&lt;IObserver&lt;Fish&gt;, Action&gt;`. El parámetro de entrada (<code>IObserver&lt;Fish&gt;</code>) es una referencia al suscriptor que está al tanto de lo que publiquemos. El valor de retorno (<code>Action</code>) es un delegado que en teoría deberíamos usar para limpiar los recursos que usamos en el flujo. </dd>
   <dt>3.</dt>
    <dd>Vamos a generar tantos peces como sean necesarios, en este caso creándolos de forma aleatoria, después, publicaremos cada uno de los peces a nuestro <code>Observer</code> mediante su método <code>OnNext</code>.  
    Esperarémos después el tiempo indicado entre cada pez.
    Antes de repetir el ciclo comprobaremos que el río es seguro, y si no lo es, lanzaremos una excepción. </dd>
   <dt>4.</dt>
    <dd>Apenas terminar el ciclo enviaremos el mensaje <code>OnCompleted</code> al <code>observer</code></dd>
   <dt>5.</dt>
    <dd>Regresaremos una <code>Action</code> vacía, recuerda que deberíamos usar este para liberar los recursos que usó nuestro flujo de datos. En este caso, no debemos liberar nada.</dd>
</dl>

Usar el método `Observable.Create<T>` es solo una de las muchas maneras en las que podemos crear un flujo, <a href="https://weblogs.asp.net/sweinstein/16-ways-to-create-iobservables-without-implementing-iobservable" target="_blank">en este sitio web</a> puedes ver 16 formas de crearlos sin necesidad de heredar tú mismo de `IObservable`.  

## Usando el río  
Vamos a crear un par de ríos para después poder suscribirnos a ellos:  

```csharp  
var rioNormal = new River(10);
var rioRapido = new River(10, waitTime: 10);
var dangerousRiver = new River(fishAmount: 10, dangerousRiver: true);
```  

Entonces ahora podemos suscribirnos y estar al tanto de qué peces pasan por el río:  

```csharp  
rioNormal.Stream()
    .Subscribe(
        onNext: fish => Console.WriteLine($"{DateTime.Now:HH:mm:ss.ffff}: {fish}"),
        onCompleted: () => Console.WriteLine("¡Terminé de pescar!")
    );
```  

<pre>
13:35:40.3923: Anglerfish, Green, 3,787.68
13:35:40.9770: Anglerfish, Green, 1,462.02
13:35:41.4831: ClownFish, Black, 1,469.41
13:35:41.9871: Blowfish, Red, 1,539.82
13:35:42.4926: Tuna, Black, 2,540.65
13:35:42.9931: Blowfish, Green, 2,905.58
13:35:43.4939: Blowfish, Red, 3,611.61
13:35:43.9994: Tuna, Black, 2,029.88
13:35:44.5048: Tuna, Yellow, 1,672.38
13:35:45.0102: Anglerfish, Black, 4,194.68
¡Terminé de pescar!
</pre>

Como vimos anteriormente, también podemos filtrar los elementos:
```csharp  
rioNormal.Stream()
    .Where(fish => fish.Color == Color.Green && fish.Weight > 3000) // Filtramos los elementos
    .Subscribe(
        onNext: fish => Console.WriteLine($"{DateTime.Now:HH:mm:ss.ffff}: {fish}"),
        onCompleted: () => Console.WriteLine("¡Terminé de pescar!")
    );
```  

<pre>
13:35:48.5546: Blowfish, Green, 3,420.28
13:35:50.0690: Anglerfish, Green, 3,974.44
¡Terminé de pescar!
</pre>  

Podemos agrupar los elementos con el método `Buffer`, que permitirá agrupar los elementos del flujo. Por ejemplo, imagina que el pescador está usando una red para 3 peces:  

```csharp  
rioNormal.Stream()
    .Buffer(3) // Agrupamos 3 elementos
    .Subscribe(
        onNext: fishCollection =>
        {
            var f = String.Join(", ", fishCollection.Select(fi => fi.Species));
            Console.WriteLine($"Atrapé {fishCollection.Count} peces ({f})");
        },
        onCompleted: () => Console.WriteLine("¡Terminé de pescar usando una red para 3 peces!")
    );
```  

<pre>
Atrapé 3 peces (Tuna, Blowfish, Tuna)
Atrapé 3 peces (Tuna, Tuna, Blowfish)
Atrapé 3 peces (Tuna, Blowfish, Blowfish)
Atrapé 1 peces (Anglerfish)
¡Terminé de pescar usando una red para 3 peces!
</pre>

Y no olvides que podemos especificarle al código que hacer en caso de que ocurra un error en el flujo, esto mediante el método (o lambda en este caso) `OnError`:

```csharp  
dangerousRiver.Stream()
    .Subscribe(
        onNext: fish => Console.WriteLine($"{DateTime.Now:HH:mm:ss.ffff}: {fish}"),
        onError: ex => Console.WriteLine($"Ocurrió un problema en el río: {ex.Message}"),
        onCompleted: () => Console.WriteLine("¡Terminé de pescar en el río peligroso!")
    );
```  

<pre>
13:35:45.5200: Anglerfish, Black, 3,171.37
13:35:46.0216: Anglerfish, Red, 3,041.97
13:35:46.5272: Anglerfish, Black, 4,589.28
13:35:47.0297: Blowfish, Black, 3,128.05
Ocurrió un problema en el río: Uh, hubo un derrame en el río
</pre>

## El pescador o `IObserver<Fish>`  
Hasta el momento hemos estado empleando lambdas para reaccionar a los mensajes de un publicador. Pero si recuerdas, en el post pasado hablé de otra interfaz que representaba al observador, recordarás también que esta interfaz especifica tres métodos. Nosotros vamos a crear una clase `Fisher` que representará al pescador:  

```csharp  
public class Fisher : IObserver<Fish>
{
    private readonly string _name;

    public Fisher(string name)
    {
        _name = name;
    }
    public void OnNext(Fish value)
    {
        Console.WriteLine($"{_name}: atrapé un {value.Species} de color {value.Color} a las {DateTime.Now:HH:mm:ss.ffff}");
    }

    public void OnError(Exception error)
    {
        Console.WriteLine($"{_name}: Oops, algo pasó {error.Message}");
    }

    public void OnCompleted()
    {
        Console.WriteLine($"{_name}: ¡Terminó la pesca!");
    }
}
```  

Y una vez que tenemos a un pescador (observador) podemos suscribirlo al flujo de peces para que esté al tanto de lo que ocurre:

```csharp  
var erik = new Fisher("Erik");
rioNormal.Stream()
    .Subscribe(erik);
```  

<pre>
Erik: atrapé un Anglerfish de color Red a las 13:35:57.6664
Erik: atrapé un Blowfish de color Yellow a las 13:35:57.6795
Erik: atrapé un Anglerfish de color Black a las 13:35:57.6923
Erik: atrapé un Blowfish de color Green a las 13:35:57.7051
Erik: atrapé un Anglerfish de color Red a las 13:35:57.7172
Erik: atrapé un Tuna de color Yellow a las 13:35:57.7301
Erik: atrapé un ClownFish de color Yellow a las 13:35:57.7429
Erik: atrapé un Blowfish de color Yellow a las 13:35:57.7558
Erik: atrapé un ClownFish de color Green a las 13:35:57.7686
Erik: atrapé un Blowfish de color Red a las 13:35:57.7815
Erik: ¡Terminó la pesca!
</pre>

Y listo, ese fue todo el ejemplo. Recuerda que el <a href=" https://github.com/ThatCSharpGuy/rx" target="_blank">código está en GitHub</a> para qe lo descargues y juegues con él. En el futuro estaré creando otro par de ejemplos sobre las bondades de la programación reactiva, entre tanto, si tienes preguntas, no dudes en hacérmelas llegar.