---
layout: post
title:  "Creando nuestra primera app con Flutter – 2da Parte"
date:   2018-09-07 13:11:00 -0600
description: En esta segunda parte aprendemos sobre los Widgets Stateless y Stateful de Flutter. También acomodamos nuestro código de mejor manera.
categories: flutter
featured_image: "/assets/img/flutter/flutter.jpg"
featured_video:
excerpt: En esta segunda parte aprendemos sobre los Widgets Stateless y Stateful de Flutter. También acomodamos nuestro código de mejor manera.
---
En la [primera parte](/creando-nuestra-primera-app-con-flutter-1ra-parte/) hicimos el setup de nuestra app con Flutter y también creamos el esqueleto básico. Ahora, 
vamos a seguir desarrollando esta pequeña aplicación y empezar a traer datos de una API pública.

## Haciendo un poco de limpieza

Por ahora toda nuestra aplicación está dentro la función main, y como te podrás dar cuenta, esto no es algo que deseamos tener, ya que es mejor tener organizado 
nuestro código para un mejor manejo de los archivos.

Vamos a hacer un poco de limpieza y de pasada también aprenderemos unos conceptos nuevos. Primero en la carpeta de lib vamos a crear una nueva carpeta llamada 
`src`  y dentro de esta carpeta creamos un archivo llamado `app.dart`.

![Flutter 1](/assets/img/posts/flutter-app-1.png "Flutter 1")

Ahora, en el archivo `app.dart` vamos a poner lo que teníamos en main pero con algunos cambios. Este archivo debe quedar así

{% highlight dart %}
import 'package:flutter/material.dart';
 
class App extends StatelessWidget {
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("Let's see images!"),
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add),
          onPressed: () {
            print("Presionando el botón");
          },
        ),
      )
    );
  }
}
{% endhighlight %}

Ahora, hay que sustituir el archivo *main.dart* por lo siguiente

{% highlight dart %}
import 'package:flutter/material.dart';
import 'package:images/src/app.dart';
 
void main() {
  var app = App();
  runApp(app);
}
{% endhighlight %}

Si corremos nuestra App, debemos ver lo mismo que hemos tenido hasta ahora

![refactoring-flutter](/assets/img/posts/refactoring-flutter.png "refactoring-flutter")

Ahora vamos a dar un vistazo al archivo `app.dart` y ver qué hemos hecho.

Lo primero que notamos es que importamos de nuevo la biblioteca de material para usar los widgets predefinidos de flutter.

Luego veremos algo importante

{% highlight dart %}
class App extends StatelessWidget
{% endhighlight %}

En este caso estamos creando una clase `App` que está extendiendo de `StatelessWidget` . Y aquí empezamos a aprender uno de los conceptos más importantes de Flutter.

En Flutter básicamente existen dos tipos de Widgets, **Stateless** y **Stateful**, y cada uno se usa dependiendo si vas a guardar un estado o no. Usamos un 
Stateless Widget cuando creamos una pantalla que contiene información que no va a cambiar, que solamente se inicializa cuando se crea, por ejemplo cuando vemos el 
detalle de un producto. Usamos el Stateful widget cuando esa pantalla o Widget va a tener que cambiar la información que muestra cada vez que el usuario haga una 
acción, por ejemplo, cuando apretamos un botón para actualizar un mensaje en la pantalla.

![stateless-stateful-diagram](/assets/img/posts/stateless-stateful-diagram.png "stateless-stateful-diagram")

En este caso usaremos un Stateless widget por que no mantendremos un estado (por ahora).

Todo Stateless widget necesita tener un método build que retorna un widget.

{% highlight dart %}
Widget build(BuildContext context) {
    return MaterialApp(
{% endhighlight %}

Este método se llama cada vez que el Widget es insertado en la estructura de la app. Este método también recibe un `BuildContext` que contiene información del 
contexto de la app, por ejemplo en qué nivel jerárquico está o información sobre la interfaz de usuario.

Por ahora dejaremos un poco los conceptos para no abrumarnos. Pero lo que presentamos son importantes para seguir con nuestra aplicación. Ahora, en nuestro archivo 
`main.dart` vemos que ya no solo importamos **material.dart** si no también el nuevo archivo que creamos. En este caso, el nombre del paquete es el nombre de nuestra app 
y de ahí solamente seguimos la ruta del archivo.

También vemos que ahora podemos crear una nueva clase llamada `App` que es el nombre que le pusimos a la clase en el archivo `app.dart`, y como ahora es un Stateless 
widget que tiene su método build, podemos correr esa clase con nuestro método `runApp(app)`

## Refactorizando Stateless a Stateful

Como te habrás dado cuenta, nuestra app es Stateless, esto quiere decir que una vez que se crea el widget no es posible cambiarlo. Y dirás, – Pero si queremos agregar imágenes en esa pantalla necesitamos que esa pantalla cambie su estado. Y estás en lo cierto, pero quería primero que se entendiera un poco sobre Stateless ya que es lo más común de encontrar y también lo más sencillo.

Ahora vamos a cambiar nuestra app a que sea Stateful para que podamos actualizarlo cada vez que apretemos el botón, y vamos a ir poco a poco para poder entender estos conceptos.

Esto es lo que vamos a hacer

![refactorizando-2](/assets/img/posts/refactorizando-2.png "refactorizando-2")

Ahora, con esos pasos vamos a empezar a modificar nuestro archivo app.dart

{% highlight dart %}
import 'package:flutter/material.dart';
 
class App extends StatefulWidget {
  createState() {
    return AppState();
  }
}
 
class AppState extends State<App> {
  int counter = 0;
 
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("Let's see images!"),
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add),
          onPressed: () {
            setState(() { // Esto es nuevo
              counter += 1;             
            });
          },
        ),
        body: Text('$counter'), // Esto es nuevo
      )
    );
  }
}
{% endhighlight %}

Como te puedes dar cuenta ahora nuestra app se extiende de `StatefulWidget` y dentro de esa clase creamos el método `createState`. Este método retorna el estado de la 
App, que es un método `AppState` que extiende de una clase abstracta llamada **State**, dentro de los signos de mayor que y menor que ponemos la App que tiene el método 
**createState** para que sepamos quien va a manejar el estado.

Si te das cuenta, la clase AppState es prácticamente igual a lo que teníamos en nuestro Stateless Widget, y esto es porque lo que estamos haciendo es que cada vez 
que la app llama a mandar `createState` se crea un nuevo widget, pero se guarda el estado que se va pasando cada vez que llamamos el método. Ahora guardamos una nueva 
variable de instancia, que es el contador, que va a estar guardando el estado de la app. Ese contador lo mostramos en el **body** del **Scaffold**, con un Widget Texto. 
Como te puedes dar cuenta tenemos un nuevo método llamado `setState` y dentro de este método tenemos el `counter += 1`. Esto es porque para actualizar el método no es 
simplemente agregar uno al contador, si no que tenemos que cambiar el estado de nuestra app mediante ese método que es el que se va pasando cada vez que se aprieta 
el botón.

Es por eso que hemos creado dos clases, una que se llama y otra que guarda el estado, para un mejor entendimiento he creado este diagrama.

![stateful_diagram](/assets/img/posts/stateful_diagram.png "stateful_diagram")

Y ahora, si corremos nuestra app

Ahora ya nuestra app está lista para actualizarse cuando apretemos el botón.

![ezgif.com-optimize](/assets/img/posts/ezgif.com-optimize.gif "ezgif.com-optimize")

Hasta ahora creo que ha sido mucha teoría, pero creo que es necesario antes de seguir para entender bien los conceptos. En la [siguiente parte](/creando-nuestra-primera-app-con-flutter-3ra-parte/) empezaremos a trabajar con JSON para traer datos.

Si tienes alguna duda o comentario sobre este tutorial, con gusto puedes escribirme a mi Twitter o los comentarios de este post.