---
layout: post
title:  "Map, Filter y Reduce en Swift"
date:   2017-04-20 09:18:41 -0600
description: Swift es un gran lenguaje, y unas de las cosas que me gusta de Swift es que puedes programar funcionalmente aún si el lenguaje no es 100% funcional. 
categories: swift
featured_image: "/assets/img/swift/swift_post_02.jpg"
featured_video:
excerpt: Swift es un gran lenguaje, y unas de las cosas que me gusta de Swift es que puedes  programar funcionalmente aún si el lenguaje no es 100% funcional. 
---

Swift es un gran lenguaje, y unas de las cosas que me gusta de Swift es que **puedes 
programar funcionalmente aún si el lenguaje no es 100% funcional**. 
Muchas de las características de Swift se parecen a otros lenguajes como Elixir y F#.

Una de las características es que las funciones son tratadas como tipos, pueden aceptar 
funciones como parámetros y retornar funciones. A
menudo a las funciones que toman como parámetros otras funciones son llamadas **funciones de orden superior**.

En este artículo vamos a enfocarnos en tres funciones que vienen en la biblioteca de Swift 
y que son muy poderosas cuando queremos manejar Arreglos.

## Map

Imaginemos que tenemos un arreglo de números enteros y a todos los elementos queremos elevarlo al cuadrado.

Generalmente haríamos una función con un bucle como el siguiente

{% highlight swift %}
// El arreglo que queremos transformar
let array = [2, 3, 4, 5, 6, 7, 8, 9, 10]

// Creamos la función que modificará los elementos del arreglo elevándolo al
// cuadrado
func squareArray(arr: [Int]) -> [Int] {
var result: [Int] = []

for x in arr {
    result.append(x * x)
  }

  return result
}

let squaredArray = squareArray(array) 
// [4, 9, 16, 25, 36, 49, 64, 81, 100]
{% endhighlight %}

La función **map** resuelve el problema de transformar los elementos de un arreglo pasándoles 
una función. Esta función itera sobre cada elemento y a cada elemento les aplica la función 
que nosotros pasemos como parámetro, esto nos ahorra usar mucho los for...in y 
además de que es más elegante. map retorna un arreglo de la misma longitud que 
el arreglo al cual le aplicamos la función.

El código anterior quedaría así

{% highlight swift %}
// El arreglo que queremos transformar
let array = [2, 3, 4, 5, 6, 7, 8, 9, 10]

// Aplicando la función map y pasándole una función, en este caso es un
// closure
let squaredArray = array.map( { x in x * x }) 
// [4, 9, 16, 25, 36, 49, 64, 81, 100]
{% endhighlight %}

En este caso pasamos la función como un closure `{ x in x * x }`, donde `x` es cada 
elemento del arreglo pasado como parámetro de la closure, y `x * x` es el cuerpo de 
la función, de esa forma pudimos transformar los elementos del arreglo sin crear ningún bucle.

## Filter

Al igual que map, **filter** es una función que itera sobre los arreglos, como su nombre lo indica, filtra los elementos de un arreglo de acuerdo a la función que nosotros le pasemos.

filter retorna un arreglo con los elementos filtrados del arreglo original, pudiendo ser de igual longitud, menor o vacío.

Supongamos que tenemos un arreglo que representa los nombres de imágenes con diferentes extensiones y queremos obtener solo los jpg.

{% highlight swift %}
// El arreglo que queremos filtrar
let images = [
  "hello.jpg",
  "world.jpg",
  "hola.png",
  "mundo.png",
  "cats.jpg",
  "dogs.png"
]

func getJpgImages(images: [String]) -> [String] {
  var results: [String] = []

  for image in images {
    if image.hasSuffix(".jpg") {
      results.append(image)
    }
  }

  return results
}

let jpgImages = getJpgImages(images) 
// ["hello.jpg", "world.jpg", "cats.jpg"]
{% endhighlight %}

Ahora usemos la función filter.

{% highlight swift %}
// El arreglo que queremos filtrar
let images = [
  "hello.jpg",
  "world.jpg",
  "hola.png",
  "mundo.png",
  "cats.jpg",
  "dogs.png"
]

let jpgImages = images.filter({ image in image.hasSuffix(".jpg") })
// ["hello.jpg", "world.jpg", "cats.jpg"]
{% endhighlight %}

Al igual que map, no tuvimos que crear ni un for-in, ni tampoco tuvimos que 
agregar el elemento que estamos filtrando, solamente pasamos nuestro 
closure y filter hace todo por nosotros.

Cabe destacar que Swift infiere el tipo de la variable gracias al arreglo original, 
por eso podemos aplicar `hasSuffix` a cada elemento porque Swift sabe que es un tipo `String`.

## Reduce

Al contrario que map y filter, **reduce** nos retorna un solo valor, que es la combinación de los elementos del arreglo al cual le aplicamos la función.

Prosigamos con el ejemplo de los números, si queremos sumar todos los elementos de un arreglo, 
haríamos algo como esto

{% highlight swift %}
// El arreglo que queremos reducir
let array = [2, 3, 4, 5, 6, 7, 8, 9, 10]

func sum(arr: [Int]) -> Int {
  var acc = 0

  for value in arr {
    acc += value
  }

  return acc
}

let arraySum = sum(array) 
// 54
{% endhighlight %}

Cuando usamos reduce, no solamente tenemos que pasar una función, si no también un acumulador, 
que es el valor inicial de nuestro contador, si es trabajo con enteros generalmente es 0, 
si son cadenas se pasa una cadena vacía. Depende de lo que quieras hacer.

Ahora el ejemplo con reduce

{% highlight swift %}
// El arreglo que queremos reducir
let array = [2, 3, 4, 5, 6, 7, 8, 9, 10]

let arraySum = array.reduce(0, combine: { acc, x in x + acc }) 
// 54
{% endhighlight %}

En este caso, pasamos como valor inicial `0`, ese valor se pasa como parámetro al closure, 
al cual le pusimos como nombre `acc`; `x` es cada elemento del arreglo, y 
el cuerpo de nuestro closure es la suma del acumulador y cada elemento del arreglo.

Como ves, esta función es un poco más elaborada, pero una vez que le entiendas, puedes hacer muchas cosas interesantes.

> Nota: Estas tres funciones todavía puede reducirse aún más gracias a la
> sintaxis de los closures, el reto es que intentes cambiarlo a su expresión
> más reducida.

## Combinando todo

Ahora que ya sabemos el uso de estas funciones, vamos a crear un pequeño programa donde usaremos las tres al mismo tiempo.

{% highlight swift %}
// Suponiendo que tenemos la siguiente estructura de ciudades
struct City {
  let name: String
  let population: Int
}

// Vamos a definir varios ejemplos de ciudades, y meterlos en un arreglo
let paris = City(name: "Paris", population: 2243)
let madrid = City(name: "Madrid", population: 3216)
let amsterdam = City(name: "Amsterdam", population: 811)
let berlin = City(name: "Berlin", population: 3397)

let cities = [paris, madrid, amsterdam, berlin]

// Como tenemos las ciudades con poca población, vamos a escribir una función
// que nos ayude a escalar la población
func scale(city: City) -> City {
  return City(name: city.name, population: city.population * 1000)
}

// Ya teniendo todo, vamos a escribir todas las ciudades cuya población
// sea mayor a 1 millón de habitantes

let citiesFilter = cities.filter { city in city.population > 1000 }
  .map(scale) // Pasamos la función que escala la población
  .reduce("City population") { result, c in
  return result + "\n" + "\(c.name): \(c.population)"
}

print(citiesFilter)

// City population
// Paris: 2243000
// Madrid: 3216000
// Berlin: 3397000
{% endhighlight %}

Listo, tenemos nuestro programa funcionando y filtrando las ciudades, sin necesidad de usar bucles, además que gracias al acumulador usado como cadena, podemos imprimir nuestras ciudades en un lindo formato.

Espero te haya convencido de que empieces a usar estas funciones en tu programa, verás cómo vas a ir haciendo un código mucho más elegante y funcional.
<blockquote>Si quieres ver esta entrada, pero con ejemplos con <strong>C#</strong> puedes visitar <a href="http://thatcsharpguy.com/post/map-filter-reduce-c-sharp/">este post</a> o si te interesa <strong>F#</strong> puedes hacerlo en <a href="http://thatcsharpguy.com/post/map-filter-reduce-f-sharp/">este post</a>.</blockquote>