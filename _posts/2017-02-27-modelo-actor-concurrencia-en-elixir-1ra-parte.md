---
layout: post
title:  "Modelo Actor Concurrencia en Elixir - 1ra Parte"
date:   2017-02-27 08:13:40 -0600
description: Primera parte del tutorial de Elixir y su modelo de actor concurrencia.
categories: elixir
featured_image: "/assets/img/elixir/elixir_post_01.jpg"
featured_video:
---

Una de las características más importantes de Erlang, y por lo consiguiente de
Elixir, es cuando trabajamos con concurrencia. Gracias a la beam, es muy fácil
trabajar con procesos concurrentes y sacar el mayor provecho de los procesadores
de varios núcleos.

Elixir usa lo que se conoce como modelo de Actor concurrencia, lo cual significa
lo siguiente:

* Cada actor es un proceso
* Cada proceso hace una tarea específica
* Para decirle algo a un proceso, necesitas  mandarle un mensaje. El proceso solo puede responder con otro mensaje.
* El tipo de mensajes que el proceso puede responder, corresponde al proceso en sí.
* Los procesos son por *pattern matching*.
* Los procesos no intercambian ninguna memoria o información con otros procesos.

A lo mejor esto pueda parecer un poco complicado al principio, pero con unos ejercicios
vamos a ir aprendiendo un poco más sobre los procesos en Elixir

## Contruyendo una aplicación del clima

Para explicar un poco más los procesos, vamos a contruir una pequeña aplicación
que nos diga la temperatura de una ciudad, usando [http://openweathermap.org/](http://openweathermap.org/). Así que para usar la API es necesario que crees una cuenta
y obtengas tu API Key para usarlo.

Vamos a crear un nuevo proyecto

{% highlight bash %}
$ mix new weather_elixir
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/weather_elixir.ex
* creating test
* creating test/test_helper.exs
* creating test/weather_elixir_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd weather_elixir
    mix test

Run "mix help" for more commands.

$ cd weather_elixir
{% endhighlight %}

Ahora en nuestro proyecto abrimos el archivo `mix.exs` para ingresar algunas
bibliotecas que vamos a utilizar.

{% highlight elixir %}
defp deps do
  [
    {:httpoison, "~> 0.9.0"},
    {:json, "~> 0.3.0"}
  ]
end
{% endhighlight %}

En la versión de Elixir 1.4 en adelante, no es necesario que pongamos esas bibliotecas
en la parte de `application` ya que elixir toma por default que lo quieres
agregar, si tu versión de Elixir es menor, entonces tendrás que agregarlo a
tus applicaciones.

{% highlight elixir %}
def application do
  [applications: [:httpoison]]
end
{% endhighlight %}

Ahora es tiempo de obtener las dependencias

{% highlight bash %}
$ mix deps.get
{% endhighlight %}

## Creando el worker

Crearemos una primera versión de nuestra aplicación en donde pediremos la temperatura
de una ciudad, esta primera versión no es concurrente pero nos sirve para conocer
nuestro código y nos da una base para ir contruyendo lo demás.

Crea un archivo `lib/worker.ex`

{% highlight elixir %}
defmodule WeatherElixir.Worker do

  def temperature_of(location) do
    result = url_for(location) |> HTTPoison.get |> parse_response
    case result do
      {:ok, temp} ->
        "#{location}: #{temp}°C"
      :error ->
        "#{location} not found"
    end
  end

  defp url_for(location) do
    # Hace el encoding de la ciudad, por ejemplo, si la ciudad es Mexico City,
    # lo cambia por Mexico%20City
    location = URI.encode(location)
    "http://api.openweathermap.org/data/2.5/weather?q=#{location}&appid=#{apikey()}"
  end

  defp parse_response({:ok, %HTTPoison.Response{body: body, status_code:
200}}) do
    # Si en nuestra respuesta obtenemos un atom :ok y dentro de la estructura HTTPoison
    # tenemos el body y el status code es 200, entonces podemos obtener nuestra variable
    # para decodificar el JSON y calcular la temperatura
    body |> JSON.decode! |> compute_temperature
  end

  defp parse_response(_) do
    # Si la respuesta es diferente entonces mandamos un atom de :error
    :error
  end

  defp compute_temperature(json) do
    # Tratamos de obtener la temperatura, la información que necesitamos de openweathermap
    # está dentro de main y temp. Le restamos 273.15 porque nos lo da en Kelvin,
    # y hacemos que solo nos muestre un decimal. Mandamos la tupla con :ok y la
    # temperatura, en caso contrario mandamos un :error
    try do
      temp = (json["main"]["temp"] - 273.15) |> Float.round(1)
      {:ok, temp}
    rescue
      _ -> :error
    end
  end

  defp apikey do
    "TU API KEY"
  end

end
{% endhighlight %}

La parte más importante está comentada. En general, lo que hace el código es
llamar a la API y obtenemos una respuesta de esa API, que gracias a HTTPoison
podemos parsear a algo que podemos manejar dentro de Elixir. De ese JSON
sacamos la información que necesitamos.

Vamos a probar este programa.

{% highlight bash %}
iex –S mix
{% endhighlight %}

Y ahora a meter algunas ciudades

{% highlight bash %}
iex(1)> WeatherElixir.Worker.temperature_of "Mexico City"
"Mexico City: 11.5°C"
iex(2)> WeatherElixir.Worker.temperature_of "Bogota"
"Bogota: 12.0°C"
iex(3)> WeatherElixir.Worker.temperature_of "Omega Plus 7"
"Omega Plus 7 not found"
{% endhighlight %}

Y si creamos una lista de ciudades

{% highlight bash %}
 iex(1)> cities = ["Mexico City", "Bogota", "Monaco", "Lima"]
["Mexico City", "Bogota", "Monaco", "Lima"]
iex(2)> cities |> Enum.map(fn city ->
...(2)>   WeatherElixir.Worker.temperature_of city
...(2)> end)
["Mexico City: 11.5°C", "Bogota: 12.0°C", "Monaco: 12.8°C", "Lima: 21.6°C"]
{% endhighlight %}

Funciona muy bien, pero hay un problema, el programa no es concurrente, para obtener
la temperatura de una ciudad espera a que termine la otra ciudad para continuar, y no
estamos sacando provecho de Elixir. 

En la [segunda parte](/elixir/modelo-actor-concurrencia-en-elixir-2da-parte/) vamos a empezar a ver los procesos de Elixir y ver cómo nos puede ayudar a tener un programa concurrente.
