---
layout: post
title:  "Creando nuestra primera app con Flutter – 1ra Parte"
date:   2018-09-05 13:11:00 -0600
description: Aprende a crear aplicaciones con Flutter. En esta primera parte vemos lo básico y creamos el esqueleto de la app, creamos apps para iOS y Android.
categories: flutter
featured_image: "/assets/img/flutter/flutter.jpg"
featured_video:
excerpt: Aprende a crear aplicaciones con Flutter. En esta primera parte vemos lo básico y creamos el esqueleto de la app, creamos apps para iOS y Android.
---
Flutter es un nuevo framework creado por Google para crear aplicaciones móviles multi plataformas. Las apps de Flutter están escritas en 
**Dart**, que también fue creado por Google para sustituir a JavaScript, y aunque es un lenguaje nuevo, si has trabajado con Java, C# o 
cualquier otro lenguaje de programación moderno es muy fácil de aprender. En este tutorial vamos a crear una pequeña app en donde 
mostraremos una lista de imágenes desde una API, con esto veremos los conceptos básicos de Flutter y empezaremos a trabajar con JSON que 
es de lo más común en nuestras aplicaciones.

# Creando nuestro entorno de desarrollo

Lo primero que debemos hacer es instalar Flutter, dependiendo del Sistema Operativo que tengas, la forma de instalar flutter se 
encuentran [aquí](https://flutter.io/docs/get-started/install). Una vez que tengamos instalado Flutter, debemos checar que todo esté bien

```
$ flutter doctor
```

Y vemos que nuestro setup esté bien

```
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel beta, v0.7.3, on Mac OS X 10.13.6 17G65, locale en-US)
[✓] Android toolchain - develop for Android devices (Android SDK 28.0.2)
[✓] iOS toolchain - develop for iOS devices (Xcode 9.4.1)
[✓] Android Studio (version 3.1)
[✓] VS Code (version 1.27.0)
[✓] Connected devices (1 available)
 
! Doctor found issues in 1 category.
```

Ahora, es tiempo de crear nuestro proyecto

```
$ flutter create images
```

Esto nos daría la siguiente estructura

```
.
├── README.md
├── android
│   ├── app
│   ├── build.gradle
│   ├── gradle
│   ├── gradle.properties
│   ├── gradlew
│   ├── gradlew.bat
│   ├── local.properties
│   └── settings.gradle
├── images.iml
├── images_android.iml
├── ios
│   ├── Flutter
│   ├── Runner
│   ├── Runner.xcodeproj
│   └── Runner.xcworkspace
├── lib
│   └── main.dart
├── pubspec.lock
├── pubspec.yaml
└── test
    └── widget_test.dart
```
Aquí el archivo más importante es `main.dart` , ya que es donde empezaremos a crear nuestra app.

# Primeros pasos

Antes que nada, vamos a correr nuestra aplicación para ver que tenemos nuestro setup correctamente, dependiendo del Editor que hayas escogido (yo escogí Visual Studio Code) hay que darle F5 para 
que corra en debug.

![Post 01](/assets/img/posts/post-01.png "Post 01")

Si todo sale bien, podremos ver la app de ejemplo funcionando en nuestro simulador de iOS, en el simulador de Android o nuestro dispositivo físico.

![App Corriendo](/assets/img/posts/app_corriendo.jpg "App Corriendo")

Si todo salió bien, ya estaremos listo para crear nuestra aplicación.

# Diseñando la aplicación de Images

Vamos a crear una pequeña aplicación que va a mostrar imágenes y su título de una API que está disponible libremente, por lo cual aprenderemos los conceptos básicos de una app en Flutter y 
podremos hacer cosas un poco más avanzadas. La aplicación que crearemos es la siguiente

![Lets-add-images.jpg](/assets/img/posts/Lets-add-images.jpg "Lets-add-images.jpg")

Tendremos un botón que, cada vez que presionemos, agregará una imagen a una lista, esta lista será deslizable para que podamos ver todas las imágenes que vayamos agregando. Al crear esta app aprenderemos:

* Sobre Widgets
* Imágenes
* Trabajar con JSON
* Eventos en Flutter

Para aprender bien todos estos conceptos, lo primero que vamos a hacer es borrar todo lo que tiene el archivo main.dart y sustituyámoslos por lo siguiente:

{% highlight dart %}
// Biblioteca que nos ayuda a crear contenido en la pantalla
import 'package:flutter/material.dart';
 
// Función 'main' que corre cuando nuestra app comienza
void main() {
  // Crea un nuevo widget de texto para mostrarlo en pantalla
  var app = MaterialApp(
    home: Text("Hola, ¡Bienvenido a Flutter!"),
  );
 
  // Toma el Widget y lo muestra en el dispositivo
  runApp(app);
}
{% endhighlight %}

Lo primero que vemos es que estamos importando una biblioteca de flutter llamada `material.dart` que nos proporciona un diseño basado en [Material](https://material.io/design/) y que se adapta a las características de iOS y 
Android (por ejemplo, en iOS la barra de título está centrada mientras que en Android se encuentra a la derecha), también nos proporciona algunas funciones básicas para construir nuestra app. En 
toda aplicación de dart, todo programa empieza a correr en el método **main**.

Por lo cual creamos un método en el cual vamos a poner nuestro código para que inicie la aplicación. Dentro del método main, crearemos un nuevo **widget**. En Flutter los widgets son el concepto más 
básico que aprenderemos. Los widgets son bloques con los cuales creamos los elementos gráficos de nuestra app. Algunos Widgets son visibles, como los botones, y otros son elementos invisibles 
pero que nos ayudan a modelar la app, como por ejemplo los márgenes.

Es hora de correr la aplicación. Si aún no has detenido la app, dale Guardar al archivo y verás una de las características más productivas de Flutter, que es el **Hot Reload**. Con esta 
característica podemos ir viendo los cambios en tiempo real sin tener que compilar el código cada vez que hagamos un cambio, esto nos ayuda mucho a ir probando la aplicación sin perder tiempo. Al 
hacer el Hot Reload, podemos ver lo siguiente

![Run iOS](/assets/img/posts/run_ios.png "Run iOS")

Bueno, no se ve tan impresionante, pero es el primer paso en nuestra jornada como Desarrolladores Flutter. Hasta este punto ya sabemos tres conceptos básicos

* Sabemos de donde vienen los widgets básicos
* Toda aplicación de Flutter y por consiguiente Dart, empiezan en el método main
* Qué son los widgets

# Mostrando un Scaffold

Si de algo nos ayuda mucho la biblioteca de material.dart, es poder tener ya widgets armados y bien hechos para crear la UI de nuestra app, y una de ellas es [Scaffold](https://docs.flutter.io/flutter/material/Scaffold-class.html).

El Scaffold es un contenedor que implementa la estructura visual de una app hecha con Material Design, este contenedor nos provee de APIs para mostrar barras, botones y otros widgets que veremos más adelante.

Si vemos nuestro mockup, necesitaremos de una barra superior que contenga el título de nuestra aplicación y un botón que nos servirá para agregar imágenes, y gracias al Scaffold será muy fácil crearlos. Vamos a sustituir la parte de Material App

{% highlight dart %}
var app = MaterialApp(
    home: Text("Hola, ¡Bienvenido a Flutter!"),
 );
{% endhighlight %}

Por lo siguiente

{% highlight dart %}
var app = MaterialApp(
    // Creamos el Scaffold 
    home: Scaffold(
      // La appBar se muestra diferente en iOS o Android
      appBar: AppBar(
        // Un título para la appBar
        title: Text("Let's see images!"),
      ),
      // Agregamos el botón flotante de nuestra app
      floatingActionButton: FloatingActionButton(
        // Como hijo del botón, agregamos un ícono (+)
        child: Icon(Icons.add),
        // ¡Nuestro primer evento!
        onPressed: () {
          print("Presionando el botón");
        },
      ),
    )
  );
{% endhighlight %}

Corremos nuestra app (Hot Reload) y veremos lo siguiente

![second_run_android](/assets/img/posts/second_run_android.png "second_run_android")

¡Vaya! Ya va tomando forma nuestra app. Con poco código creamos la estructura principal de la aplicación. Aunque el código no es mucho, si tiene varias cosas que hay que apuntar. Como ya habíamos 
hablados, el **Scaffold** nos permite tener una estructura predefinida para usar y agregar otros widgets.

{% highlight dart %}
appBar: AppBar(
  // Un título para la appBar
  title: Text("Let's see images!"),
),
{% endhighlight %}

El Scaffold nos permite agregar una barra superior en el cual podemos poner otros Widgets, en este caso, como título ponemos un widget de texto.

{% highlight dart %}
floatingActionButton: FloatingActionButton(
  // Como hijo del botón, agregamos un ícono (+)
  child: Icon(Icons.add),
    // ¡Nuestro primer evento!
    onPressed: () {
      print("Presionando el botón");
    },
),
{% endhighlight %}

Al igual que Scaffold nos permite agregar una barra superior fácilmente, también tiene la opción de tener un botón flotante, este botón acepta como Widget un **FloatingActionButton**, que permite 
tener un hijo, en el cual nosotros pusimos un ícono (también un Widget) y un evento **onPressed:** que acepta una función anónima en donde podemos poner acciones que se ejecutarán cuando se presiona 
el botón. Este evento lo utilizaremos más adelante.

Con lo que hemos hecho hemos avanzado en crear nuestra primera app con Flutter, en la [siguiente parte](/creando-nuestra-primera-app-con-flutter-2da-parte/) vamos a seguir avanzando; limpiaremos un poco nuestra app para tener todo más organizado.

Si tuviste algún problema o tienes alguna duda, puedes ponerlo en los comentarios y con gusto te ayudaré.