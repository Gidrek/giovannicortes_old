---
layout: post
title:  "Creando nuestra primera app con Flutter – 3ra Parte"
date:   2018-09-13 13:11:00 -0600
description: En esta tercera y última parte del tutorial de Flutter terminamos la app de imágenes y le agregamos un poco de diseño a nuestros widgets.
categories: flutter
featured_image: "/assets/img/flutter/flutter.jpg"
featured_video:
excerpt: En esta tercera y última parte del tutorial de Flutter terminamos la app de imágenes y le agregamos un poco de diseño a nuestros widgets.
---

Ya tenemos el esqueleto de nuestra app gracias a la [primera](/creando-nuestra-primera-app-con-flutter-1ra-parte/) y [segunda parte](/creando-nuestra-primera-app-con-flutter-2da-parte/)
del tutorial de Flutter. Ahora es tiempo de empezar a trabajar con la API que vamos a utilizar para agregar datos a nuestra aplicación. La API que vamos a utilizar 
es [https://jsonplaceholder.typicode.com/](https://jsonplaceholder.typicode.com/), el cual es un servicio REST que contiene datos fake que nos servirá para probar nuestra app.

En sí, la URL que vamos a utilizar es [https://jsonplaceholder.typicode.com/photos](https://jsonplaceholder.typicode.com/photos), que nos trae datos como el siguiente

```json
[
  {
    "albumId": 1,
    "id": 1,
    "title": "accusamus beatae ad facilis cum similique qui sunt",
    "url": "https://via.placeholder.com/600/92c952",
    "thumbnailUrl": "https://via.placeholder.com/150/92c952"
  },
  {
    "albumId": 1,
    "id": 2,
    "title": "reprehenderit est deserunt velit ipsam",
    "url": "https://via.placeholder.com/600/771796",
    "thumbnailUrl": "https://via.placeholder.com/150/771796"
  },
  {
    "albumId": 1,
    "id": 3,
    "title": "officia porro iure quia iusto qui ipsa ut modi",
    "url": "https://via.placeholder.com/600/24f355",
    "thumbnailUrl": "https://via.placeholder.com/150/24f355"
  },
]
```

Utilizaremos `url` para tener la imagen fake que tiene la API.

Si usamos la url de photos con el id del album, solo nos traerá la info de ese album

[https://jsonplaceholder.typicode.com/photos/1](https://jsonplaceholder.typicode.com/photos/1)

```json
{
  "albumId": 1,
  "id": 1,
  "title": "accusamus beatae ad facilis cum similique qui sunt",
  "url": "https://via.placeholder.com/600/92c952",
  "thumbnailUrl": "https://via.placeholder.com/150/92c952"
}
```

Con esta herramienta en mano, vamos a empezar a trabajar.

## Creando el modelo de la imagen

Dado que vamos a trabajar con datos de internet que no siempre son manejables de forma sencilla, lo más recomendable es crear modelos en Dart para así aprovechar 
las características que nos da el lenguaje de tipo estático. De esa forma podemos manejar mejor la información y los errores.

Dentro de la carpeta src vamos a crear otra carpeta llamada `models` y dentro de esa carpeta creamos un archivo `image_model.dart`

![new-model-folder](/assets/img/posts/new-model-folder.png "new-model-folder")

Vamos a crear nuestro modelo

```dart
class ImageModel {
  int id;
  String url;
  String title;
}
```

Aquí creamos una clase llamada `ImageModel` que contiene 3 propiedades: id, url y title. Con esas propiedades vamos a trabajar para crear nuestra interfaz.

Ahora, queremos que cuando creamos un objeto de `ImageModel` ya contenga estas propiedades inicializadas, por lo cual vamos a agregar un constructor para que cuando 
creamos el objeto de **ImageModel** ya tenga datos asociados a ese objecto. Agreguemos la siguiente línea de código a la clase

```dart
ImageModel(this.id, this.url, this.title);
```

Lo que acabamos de escribir es una forma sencilla de crear un constructor en Dart, podríamos haber escrito

```dart
ImageModel(int id, String url, String title) {
  this.id = id;
  this.url = url;
  this.title = title;
}
```

Pero esto es tan común que Dart nos permite hacerlo de la manera en que lo hicimos, haciendo el código más fácil de leer y de crear.

Ya que tenemos nuestro constructor podemos empezar a trabajar con los datos de la API, aunque podemos hacer la inicialización de una forma más sencilla. Si vemos la 
respuesta que nos da el JSON sabremos que nos va a dar el **id**, el **title** y la **url** por lo cual estaremos repitiendo un poco el código si hacemos todo a mano. Pero en 
Dart podemos crear lo que se conoce como Factories que es una forma de inicializar un objeto pasándole datos de una estructura, en nuestro caso la estructura es un 
JSON.

Vamos a agregar a nuestra clase esta **factory** para crear nuestros objetos a través del json que le pasemos

```dart
ImageModel.fromJson(Map<String, dynamic> parsedJson) {
  id = parsedJson["id"];
  url = parsedJson["url"];
  title = parsedJson["title"];
}
```

En este caso creamos un método llamado `fromJson` que va a actuar como nuestro **factory**. Como el json que le pasemos sabemos que es un Mapa (una estructura con una 
cadena como llave y un valor dinámico), le pasamos el `Map<String, dynamic> parsedJson`, ya que sabemos que la llave siempre será una cadena y lo que cambia es el 
valor, que puede ser entero, cadena, etc.

Gracias a que Dart tiene un tipado estático, ya sabemos que el id es entero y lo demás son cadenas. Del json que le pasemos solamente va a agarrar los datos que 
queramos.

## Obteniendo datos de la API

Ya tenemos todo listo para obtener los datos de la API con Flutter. La idea es de que cada vez que el usuario presione el botón, haga un request a la API, 
transformamos el JSON y mostramos esos datos en nuestra App.

Vamos a nuestro archivo `app.dart` y en nuestra clase `AppState` agregamos un nuevo método.

```dart
fetchImage()  {
}
```

Por ahora estará vacío, ya luego lo llenaremos con más código. Ahora, hay que cambiar la parte de `onPressed` ya que cada vez que el usuario presione el botón, 
llamaremos nuestra función, esto debe quedar así

```dart
floatingActionButton: FloatingActionButton(
   child: Icon(Icons.add),
   onPressed: fetchImage,
),
```

Si te das cuenta y si has usado otros lenguajes, en este caso, cuando llamamos al método `fetchImage()` en el `onPressed` lo ponemos sin paréntesis. Esto es porque si 
le ponemos paréntesis significa que cuando se crea el AppState se va a activar y no queremos esto, si no solamente cuando se presione el botón. Entonces al no 
ponerle paréntesis no se manda a llamar automáticamente si no cada vez que el evento `onPressed` se activa.

Ahora sí viene lo bueno, ya es hora de empezar a trabajar con la API. Para eso necesitamos agregar una biblioteca que se va a encargar de hacer el llamado HTTP y 
obtener los resultados.

Al inicio del archivo hay que hacer tres imports.

```dart
import 'package:http/http.dart' show get;
import 'dart:convert';
import 'package:images/src/models/image_model.dart';
```

En este caso, solo usaremos el get de la biblioteca de `http.dart` por lo cual solo mostramos esa función. Usaremos `dart:convert` para hacer un parse del JSON. Y 
también importamos nuestro modelo.

Hay que modificar  el método `fetchImage` para que quede de la siguiente manera

```dart
fetchImage() async {
  counter++;
  var response = await get('https://jsonplaceholder.typicode.com/photos/$counter');
  var imageModel = ImageModel.fromJson(json.decode(response.body));
}
```

Si nos damos cuenta, cambiamos nuestro método a que sea asíncrono con  `async`. Cuando queremos obtener un dato de internet no sabemos cuánto va a tardar en obtener 
la respuesta, porque son muchos factores como la velocidad de conexión, si el servidor responde rápido o no, etc. Por lo cual, para no bloquear nuestra aplicación 
usamos métodos asíncronos.

Ya dentro del método aumentamos en 1 el contador para ir obteniendo las imágenes. Luego tenemos la variable `response` que obtiene los datos del **get**, como no sabemos 
cuánto va a tardar la respuesta, usamos `await` para esperarnos a que nos de los datos.

Una vez que tenemos los datos, ya podemos tener nuestro objeto gracias al constructor que creamos anteriormente con el `fromJson`, no sin antes hacer un decode del 
cuerpo de la respuesta.

En realidad este método no es tan complicado, pero si hace uso de unos conceptos que no son tan conocidos. Solo piensa en que para obtener datos de internet tenemos 
que esperarnos para poder usar esos datos como cualquier otra variable.

## Una lista para nuestros modelos de imágenes

Ya podemos tener nuestros objetos de imágenes desde la API, pero en nuestra app no solo vamos a usar una imagen, si no muchas, por lo cual necesitaremos una 
variable para guardar una lista de imágenes.

Agrega la siguiente variable debajo del counter

```dart
List<ImageModel> images = [];
```

Creamos una variable `images` que va a estar guardando una lista de `ImageModel`.

Ahora en nuestro método **fetchImage** hay que agregar al final

```dart
setState(() {
  images.add(imageModel);
});
```

Como ya explicamos en la [segunda parte](/creando-nuestra-primera-app-con-flutter-2da-parte/), para modificar el estado de nuestra aplicación necesitamos usar el método `setState` ya que de esa forma el estado se va 
pasando en cada iteración.

# Creando una lista de Widgets

Ya tenemos nuestra lista de imágenes, ahora nos queda hacer una lista de Widgets para que podamos mostrarlo al usuario.

Vamos a crer una nueva carpeta en **src** llamada **widgets** y dentro de esa carpeta creamos un archivo llamado `image_list.dart`

![image-list-create-file](/assets/img/posts/image-list-create-file.png "image-list-create-file")

En el archivo escribe lo siguiente

```dart
import 'package:flutter/material.dart';
import 'package:images/src/models/image_model.dart';
 
class ImageList extends StatelessWidget {
 
}
```

Hacemos un esqueleto de la lista de imágenes que vamos a utilizar. Volvamos al archivo app.dart para hacer unos cambios y usar nuestra lista de imágenes.

En el archivo hay que agregar un import

Y sustituir el **body** de nuestro **Scafold** con la lista de imágenes

```dart
body: ImageList(images)
```

¡Pero espera, estamos mandando un parámetro! Sí, este parámetro nos servirá para hacer la lista de imágenes en el widget que creamos anteriormente, por lo que hay que modificar nuestro archivo `image_list.dart` para que quede de la siguiente forma

```dart
import 'package:flutter/material.dart';
import 'package:images/src/models/image_model.dart';
 
class ImageList extends StatelessWidget {
  final List<ImageModel> images;
  
  ImageList(this.images);
 
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: images.length,
      itemBuilder: (context, int index) {
        return Text(images[index].url);
      }
    );
  }
}
```

Dado que un `StatelessWidget` una vez que se crea ya no se puede modificar, sus variables deben ser `final`, en este caso creamos una lista de `ImageModel`. Y creamos el 
constructor para poder recibir las imágenes.

Por último creamos el método build para construir nuestro Widget. Lo que vamos a retornar es un `ListView` que de acuerdo a su [documentación](https://docs.flutter.io/flutter/widgets/ListView-class.html), el builder necesita 
saber cuántos datos se van a dibujar y qué es lo que se va a dibujar.

¡Uff! Hasta ahora hemos hecho muchos cambios, ahora es tiempo de correr la app para ver si tenemos los datos de internet. Cada vez que presiones el botón te debe 
estar mostrando una URL diferente.

![images-urls](/assets/img/posts/images-urls.png "images-urls")

## Cambiando las URLs por Imágenes

Ya tenemos una app funcional que trae datos de internet. Pero nuestra app necesita imágenes. Pues Flutter nos la pone muy fácil, solo cambiamos el return de nuestro 
`image_list.dart`

{% highlight dart %}
return Image.network(images[index].url);
{% endhighlight %}

Como vez, fue muy sencillo, usamos el Widget `Image` y como traemos la imagen de internet usamos el método `network` y pasamos la url de la image.

Ahora corre de nuevo la app para ver nuestras imágenes

![app-images-completed-1](/assets/img/posts/app-images-completed-1.png "app-images-completed-1")

¡Listo! Ya tenemos imágenes. Ahora nuestra app está completa y es 100% funcional. Pudimos usar Flutter para poder consumir una REST API y poder mostrar imágenes de 
forma dinámica.

## Añadiendo un poco de diseño

Hasta aquí podríamos dejar el tutorial, ya que llegamos al objetivo principal que era mostrar imágenes. Pero podemos mejorar la app gracias a los widgets de 
Posición y Contenedores de Flutter.

Esta vez iremos un poco más rápido y podremos ahondar en estos temas si gustas en otros tutoriales.

Primero vamos a cambiar el return para que no regrese solamente imágenes, ahora usamos un contenedor que tiene como hijo la imagen y también decoramos con bordes, 
márgenes  y paddings

```dart
return ListView.builder(
  itemCount: images.length,
  itemBuilder: (context, int index) {
    return Container(
      decoration: BoxDecoration(
        border: Border.all(color: Colors.grey)
      ),
      padding: EdgeInsets.all(20.0),
      margin: EdgeInsets.all(20.0),
      child: Image.network(images[index].url),
    );
  }
 );
```

Hasta ahora nos quedaría así

![app-completed-image-2](/assets/img/posts/app-completed-image-2.png "app-completed-image-2")

Como ves ya está mejor.

Vamos a mejorar la imagen con un poco de información. Debajo del método build vamos a crear un nuevo widget, esto es para tener mejor organizado nuestro código

```dart
Widget buildImage(ImageModel image) {
  return Container(
    decoration: BoxDecoration(border: Border.all(color: Colors.grey)),
    padding: EdgeInsets.all(20.0),
    margin: EdgeInsets.all(20.0),
    child: Column(
      children: <Widget>;[
        Padding(
          child: Image.network(image.url),
          padding: EdgeInsets.only(bottom: 8.0)
        ),
        Text(image.title)
      ]
    ),
  );
}
```

Y sustituir el return del build

```dart
return buildImage(images[index]);
```

Y ya corriendo la app nos mostrará

![app-images-completed-3](/assets/img/posts/app-images-completed-3.png "app-images-completed-3")

Ya se ve mucho mejor. Ahora también sabemos cómo posicionar y manejar los contenedores.

En Flutter hay mucho más que aprender pero con esto ya tenemos lo básico para empezar a crear aplicaciones con Dart y Flutter.

¿Te gustó el tutorial? Si es así comparte para que llegue a más personas. Si quieres seguir aprendiendo sobre Flutter puedes pedirlo en los comentarios. Igualmente 
si algo no te sale puedes decirlo y con gusto te ayudaré.

Pueden obtener el código completo en [https://github.com/Gidrek/images-flutter-tutorial](https://github.com/Gidrek/images-flutter-tutorial)