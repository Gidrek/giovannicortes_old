---
layout: post
title:  "Modelo Actor Concurrencia en Elixir - 3ra Parte"
date:   2017-03-06 08:11:34 -0600
categories: elixir
featured_image: "/assets/img/elixir/elixir-logo.jpg"
featured_video:
---

[En la parte 1](/elixir/modelo-actor-concurrencia-en-elixir-1ra-parte/) 
creamos nuestro programa del clima. [En la parte 2](/elixir/modelo-actor-concurrencia-en-elixir-2da-parte/) ya hicimos que nuestro
programa fuera concurrente. Ahora vamos a modificar nuestro programa
para que un actor pueda recolectar los datos y poder manipularlos, ya que
mandarlos a la shell no nos sirve de mucho.

El actor que vamos a crear debe saber cúantos mensajes son los esperados. En
otras palabras, este actor va a guardar el estado. 

Para entenderle mejor, crearemos un archivo en `lib/coordinator.ex`

{% highlight elixir %}
defmodule WeatherElixir.Coordinator do
  def loop(results \\ [], results_expected) do
    receive do
      {:ok, result} ->
        new_results = [result|results]
        if results_expected == Enum.count(new_results) do
          send self(), :exit
        end
        loop(new_results, results_expected)
      :exit ->
        IO.puts(results |> Enum.sort |> Enum.join(", "))
      _ ->
        loop(results, results_expected)
    end
  end
end
{% endhighlight %}

Primeramente, en nuestro coordinador agregamos un loop que va a estar 
escuchando y va a poder recibir como mensaje una tupla con un atom y los
resultados de nuestro worker, también va a poder recibir un atom `:exit` en
en cual va a poner los resultados, los va a ordenar y los va a unir en una
cadena para poder leerlos.

Si llega algún otro mensaje, entonces simplemente va a correr el loop de 
nuevo.

Vamos a ver más de cerca nuestro loop que recibe la tupla, ya que es lo que 
hace que nuestro coordinador vaya recibiendo los resultados.

{% highlight elixir %}
defmodule WeatherElixir.Coordinator do
  def loop(results \\ [], results_expected) do
    receive do
      {:ok, result} ->
        # Agrega el resultado recibido a la lista de resultados, usamos el
        # [head|tail] de elixir, que nos ayuda a agregar al principio de una
        # lista
        new_results = [result|results]

        # Checa si todos los resultados han sido recolectados
        if results_expected == Enum.count(new_results) do
          # Si todos los resultados han sido recolectados, entonces manda
          # el mensaje de salida para que imprima los datos
          send self(), :exit
        end
        # Al final mandamos a llamar de nuevo al loop, para que siga
        # escuchando nuevos mensajes, este loop recibe los nuevos resultados
        # pero mantiene los resultados esperados, ya que este va a ser
        # siempre el mismo
        loop(new_results, results_expected)
  end
end
{% endhighlight %}

Este segmento de código lo que hace es estar recibiendo los resultados del
worker y agregarlo a la lista, cuando no hay más resultados entonces se sale.

Listo, ahora solo nos falta usar a neustro coordinador junto al worker.
Escribamos esto en nuestro archivo `lib/elixir_weather.ex`.

{% highlight elixir %}
defmodule WeatherElixir do
  
  def temperatures_of(cities) do
    # Creamos un proceso en donde va a trabajar nuestro coordinador
    # Mandamos una lista vacía que es donde se va a ir agregando las ciudades,
    # y los resultados esperados son la cantidad de ciudades que pasemos
    coordinator_pid =
      spawn(WeatherElixir.Coordinator, :loop, [[], Enum.count(cities)])

    # Recorremos cada ciudad  
    cities |> Enum.each(fn city ->
      # Creamos un proceso ahora del worker, mandando la función que va
      # a estar haciendo el loop
      worker_pid = spawn(WeatherElixir.Worker, :loop, [])
      # El loop del worker recive un pid y una ciudad, por lo cual
      # mandamos ese mensaje.
      send(worker_pid, {coordinator_pid, city})
    end)
  end
end
{% endhighlight %}

Es un código no tan complicado, creamos el proceso del coordinador que va
a estar escuchando cada vez que un proceso de un worker le mande un mensaje.

Cuando hacemos esto `send(worker_pid, {coordinator_pid, city})`, mandamos un
mensaje al worker, pero  con el pid del coordinador, si recuerdas, este es 
el mensaje que esta esuchando el worker

{% highlight elixir %}
  def loop do
    receive do
      { sender_pid, location} ->
        # Aquí es donde mandamos a nuestro coordinador la temperatura de
        # la ciudad
        send(sender_pid, {:ok, temperature_of(location)})
...
{% endhighlight %}

Ahora es tiempo de usar nuestro programa, `iex -S mix`

{% highlight shell %}
iex(1)> cities = ["Mexico City", "Guadalajara", "Caracas", "Cali", "London", "New York", "Tokio", "Madrid"]
["Mexico City", "Guadalajara", "Caracas", "Guadalajara", "Cali", "London",
 "New York", "Tokio", "Madrid"]
iex(2)> WeatherElixir.temperatures_of(cities)
:ok
Cali: 25.0°C, Caracas: 22.4°C, Guadalajara: 13.0°C, London: 9.5°C, Madrid: 18.0°C, Mexico City: 13.9°C, New York: -1.2°C, Tokio: 2.5°C
{% endhighlight %}


Como vez, obtuvimos los resultados esperados, de una forma más rápida
porque los procesos fueron concurrentes, y también ordenados alfabeticamente
gracias a nuestro coordinador.


¡Felicidades! Has creaeado tu primer programa concurrente en Elixir y has
usado uno de las características más importantes de Erlang y Elixir.

Si tienes alguna duda puedes escribirlo aquí o mandarme un mensaje en [Twitter](https://twitter.com/giovanni_cortes). El repositorio
de este proyecto está en [Github](https://github.com/Gidrek/weather_elixir)
