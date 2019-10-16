---
layout: post
title:  "Modelo Actor Concurrencia en Elixir - 2da Parte"
date:   2017-03-01 08:13:40 -0600
description: Segunda parte del tutorial de Elixir y su modelo de actor concurrencia.
categories: elixir
featured_image: "/assets/img/elixir/elixir_post_02.jpg"
featured_video:
---

[Ya tenemos nuestra aplicación funcionando](/elixir/modelo-actor-concurrencia-en-elixir-1ra-parte/), nos trae la información que queremos
y también muestra información de error cuando ponemos una ciudad que no exite. Con
base en esto podemos ir agregando características para hacer de nuestro
proyecto un poco más concurrente.

## Creando procesos para concurrencia

Vimos que nuestra aplicación presenta un problema, ya que aunque funciona bien, no es
concurrente y tenemos que esperar a que una acción termine para que empieze la otra. Checando
nuestro programa, nos podemos dar cuenta que el pedir la temperatura de una ciudad es
independiente de pedir la temperatura en otra ciudad, entonces es una muy buena forma
de empezar a empacar la función de `WeatherElixir.Worker.temperature_of` en un proceso.

Primero vamos a agregar una función loop a nuestro worker que se encargará de
crear los procesos para cada ciudad

```elixir
defmodule WeatherElixir.Worker do

  def loop do
    receive do
      { sender_pid, location} ->
        send(sender_pid, {:ok, temperature_of(location)})
      _ -> 
        IO.puts "Don't know how to process this message"
    end
    loop()
  end

  ...
```


Vamos a explicar un poco los procesos para ir entendiendo que vamos haciendo.
Corre de nuevo `iex -S mix` para que probemos la nueva función.

```bash
iex(1)> pid = spawn(WeatherElixir.Worker, :loop, [])
#PID<0.190.0>
```

Usamos una función que viene integrada en Elixir llamada `spawn`, esta función
es especial ya que nos crea los procesos. Cuando creamos un proceso esta función 
nos regresa un ID con el cual podemos identificar el proceso que creamos. La función
recibe como parámetros un módulo, seguido de un atom que representa el nombre de la
función y al final una lista de parámetros.

## Recibiendo mensajes

Como dijimos anteriormente, el pid es la referencia a un proceso, con el cual
podemos interactuar. Una función puede recibir mensaje de otros procesos, siempre
y cuando estén dentro de un bloque `receive`. Vamos a darle un vistazo a nuestra
función loop

```elixir
  def loop do
    receive do
      { sender_pid, location} ->
        send(sender_pid, {:ok, temperature_of(location)})
      _ -> 
        IO.puts "Don't know how to process this message"
    end
    loop()
  end
```

Tenemos nuestro bloque `receive` en donde nos encontramos con una estructura
similar a un `case`, esto quiere decir que va a recibir una serie de parámetros
y va a hacer **pattern matching** con el primer patrón que encuentre. En nuestro
caso si recibe como mensaje una tupla de dos elementos entonces va a hacer
pattern matching con la primera opción, y va a dar un mensaje de error con cualquier
otro mensaje.

Es muy importante que tomes en cuenta la posición en que pones los patrones, ya que
Elixir toma siempre el primero que encuentre, supongamos que ponemos este patrón
al principio `_ -> `, esto va a hacer que siempre haga match con los mensajes y nunca
va a mandar a hacer la segunda opción, por eso es recomendable que pongas el patrón 
digamos que por defecto, al final.

Cuando recibimos mensajes en nuestros procesos, estos llegan a un `mailbox` del proceso
que guarda y ejecuta cuando es llamado. Todo esto depende de lo que queramos hacer
con nuestros mensajes recibidos.

## Mandando mensajes

Una vez que ya sabemos cómo recibir mensajes, entonces no queda más que enviar
mensajes a esos procesos, para mandar mensajes usamos otra función especial que se
llama `send/2`. El primer argumento es el pid del proceso al cual le mandaremos 
mensaje, por eso es importante guardar ese pid, el segundo argumento es el mensaje
en si que andamos mandando.

```elixir
    receive do
      { sender_pid, location} ->
        # Mandando un mensaje con sender
        send(sender_pid, {:ok, temperature_of(location)})
    end
```

En nuestra función de loop estamos mandando un mensaje con la función sender, el
cual usa el pid que recibimos y mandar a llamar la función `temperature_of`. Ya con
esto podemos empezar a utlizar nuestras funciones. Vamos al `iex`

```bash
iex(2)> send(pid, {self(), "Mexico City"})
{#PID<0.183.0>, "Mexico City"}
```

¿Te acuerdas que habiamos creado un proceso de nuestra función loop? Mandamos
un mensaje a ese proceso, esa función espera la siguiente estructura `{ sender_pid, location}`,
nosotros mandamos esa estructura, pero si te das cuenta no mandamos el pid en si, si no que
usamos `self()`, esta función lo que hace es obtener el pid del proceso en que se está ejecutando 
la función. En nuestro caso, el pid es la sesión de `iex`, de esta forma le decimos al worker
que nos regrese todas las respuesta a nuestra sesión de iex, pero no obtuvimos lo que deseábamos, 
que es la temperatura de la Ciudad de México, si no que recibimos el mismo mensaje que mandamos.

Como te había dicho anteriormente, todos los procesos tienen un mailbox donde se almacenan
los mensajes, y no son llamados hasta que se haga uso de ellos o los forcemos, para forzar
a que salgan del mailbox podemos usar la función `flush/0`

```elixir
iex(8)> flush()
{:ok, "Mexico City: 12.1°C"}
:ok
```

Es tiempo de crear la lista de ciudades

```bash
iex(1)> cities = ["Mexico City", "Bogota", "Monaco", "Lima"]
["Mexico City", "Bogota", "Monaco", "Lima"]
iex(2)> cities |> Enum.each(fn city ->
...(2)>   pid = spawn(WeatherElixir.Worker, :loop, [])
...(2)>   send(pid, {self(), city})
...(2)> end)
:ok
iex(3)> flush()
{:ok, "Mexico City: 19.7°C"}
{:ok, "Monaco: 14.3°C"}
{:ok, "Bogota: 14.0°C"}
{:ok, "Lima: 23.1°C"}
:ok
```

¡Perfecto! Ya hemos creado procesos para obtener la temperatura. Si te das cuenta, los
resultados no están en el mismo orden en que se mandaron, esto es porque no todos los procesos
terminan en el mismo orden y lo vamos recibiendo conforme se completen.

Veamos nuestra función loop un poco más

```elixir
  def loop do
    receive do
      { sender_pid, location} ->
        send(sender_pid, {:ok, temperature_of(location)})
      _ -> 
        IO.puts "Don't know how to process this message"
    end
    loop() ## <- ¡Es recursivo! 
  end
```
Nuestra función es recursiva. Esto es muy importante si queremos que este proceso esté
recibiendo más de un mensaje, porque cuando un proceso en Elixir recibe un mensaje
y lo procesa este proceso termina y ya no podremos tener acceso a el. Por lo cual llamamos
de nuevo a la función de tal manera que se quede escuchando el tiempo que viva nuestro
programa.

Por ahora hemos creado una función que puede estar recibiendo mensajes y creando procesos, 
pero hemos estado haciendo de forma manual todo esto, en la [siguiente parte](/elixir/modelo-actor-concurrencia-en-elixir-3ra-parte/) crearemos
un actor nuevo que nos ayude a coordinar todas estas llamadas y también a que podamos hacer
uso de las ciudades y temperaturas sin estar haciendo flush en nuestro iex.
