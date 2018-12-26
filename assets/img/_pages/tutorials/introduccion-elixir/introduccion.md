---
layout: default
title: Introducción - Introducción a Elixir
description: Una pequeña introducción al curso de Elixir
permalink: /introduccion-a-elixir/introduccion/
---

# Introducción a Elixir, ¿Por qué aprender Elixir?

<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/N2qnLacPwRw"></iframe>
</div>

### Descargar video

[Descargar](https://mega.nz/#!pmhS0DrZ!4alygowi0UfiSeCNUHwOll86WNc-CVatvtGY0twcrRM)

## Notas del curso

Actualmente existen millones de dispositivos conectados a internet. No solo computadoras, smartphones y servidores, si no también aparatos que antes no tenían nada que ver con internet, como televisores, cámaras, equipo médico.

Si deseas construir un sitio web exitoso debes estar preparado para soportar millones de peticiones diariamente sin que haya caídas por algún bug, que cuando actualices no tengas que dar de baja el servidor dejando sin servicio a los que entrar a tu página, que sea fácilmente escalable y distribuido. Esto es muy difícil de hacer con los lenguajes y frameworks tradicionales, pero es en esto que Elixir viene a ayudarnos.

Elixir ha surgido como un lenguaje de programación nuevo y que toma muchas cosas de Erlang. Ambos lenguajes corren en la máquina virtual de Erlang llamada BEAM. Podríamos ver esta relación similar a lo que es Java con Groovy, ambos son diferentes lenguajes, pero los dos corren la máquina virtual de Java (JVM).

Para entender de Elixir hay que entender algunas cosas de Erlang como plataforma, cosas que lo hacen muy interesante para la web de hoy.

### Historia de Erlang

Erlang fue creado en la década de los 80 por la compañía Ericsson, ellos querían una forma de controlar sus swtiches en la red telefónica sin que hubiera caídas del servicio y que soportara millones de llamadas, además de que en caso de que hubiera alguna actualización no tuvieran que dar de baja todo el sistema, esto es, querían actualizaciones en caliente.

Todo esto llevo a los creadores de Erlang las siguientes metas de diseño:

* **Concurrente**. Esto es porque millones de personas usan la red telefónica al mismo tiempo, Erlang necesita manejar todo esto de manera eficaz.
* **Tolerancia a fallos**. Si hay algún error en una llamada, este no afecta a otros, además de que puede volver a poner en funcionamiento la línea de manera elegante y transparente para los desarrolladores.
* **Distribuido**. Erlang no solo se ejecuta y se comunica con las otras instancia dentro de la misma máquina, si no también tiene la característica de que puede comunicarse con diferentes máquinas en cualquier parte del mundo a la que esté conectado el dispositivo.
* **Alta disponibilidad**. No importa que haya actualizaciones o algún fallo en alguna línea, el sistema siempre va a estar disponible.

Estas metas que se hicieron para los switches de Ericsson actualmente se siguen aplicando, como podrás darte cuenta todas esas metas son también deseables para los sitios web en la actualidad, ya que hay millones de conexiones al sitio y necesitamos que este disponible y que en caso de algún bug este no se caiga.

### Características de Erlang

A parte de las metas para lo que fue creado Erlang, también esta plataforma posee unas características que lo hacen muy adecuado a la hora de programar, aunque también muchos de los conceptos son diferentes a lo que veníamos acostumbrados, por lo tanto habrá que tener un cambio de mentalidad pero es muy agradable.

**Estados Inmutables.**

Significa que cuando algo es creado en la memoria esto no puede ser alterado, solamente puede ser copiado. Esto nos permite manipular datos y tener la seguridad de que no va a ser afectado por algo más que por lo que nosotros le digamos, esto permite capturar errores de una manera más sencilla. Esto también hace que la concurrencia de Erlang no produzca efectos indeseados.

**Lenguaje funcional, no orientado a objetos**

En Erlang no existe lo que llamamos programación orientado a objetos, no hay clases, no hay herencia, todo es creado con funciones y agrupado en módulos.

**Procesos**

La máquina virtual de Erlang soporta procesos, estos procesos son análogos a los procesos del sistema operativo, pero son muchos más livianos y son manejados por la BEAM. Estos procesos corren concurrentemente y no afectan a otros.

**Comunicación por mensajes**

Al ser los procesos independiente uno de otros, la única forma de comunicarse es por medio de mensajes que se envían entre ellos. Esto también nos asegura que cada proceso tenga su memoria interna y sus propios datos, de tal forma que no puede ser corrompida por otro proceso y en caso de que un proceso falle no afecta a los demás.

**Supervisores**

Erlang maneja el concepto de supervisores que se encargan de monitorear los procesos, cada supervisor puede monitorear a varios hijos y estos a su vez pueden ser monitoreados por otros supervisores. En caso de que algún proceso falle, los supervisores se encargan de reiniciar el proceso y este vuelve a estar disponible para ser utilizado.

### Entonces, ¿por qué Elixir?

Elixir surge como una alternativa al lenguaje Erlang, usando la misma máquina virtual y así obtener todos sus beneficios, Elixir es un lenguaje mucho más amigable para aquellos que quieran aprender a programar con la plataforma Erlang, además de que agrega varias características que no tiene Erlang como metaprogramación y polimorfismo. No hay nada que puedas hacer en Erlang que no puedas hacer en Elixir. Además de que puedes accesar a todas las bibliotecas de Erlang de forma transparente y sin penalizaciones de performance.

Una de las cosas que tiene Elixir es que el código es más fácil de leer y nos evita repetir muchas cosas. Por ejemplo, este es un programa que suma dos números y lo hace por medio de procesos.

{% highlight erlang %}
-module(sum_server).

-behaviour(gen_server).
-export([
  start/0, sum/3,
  init/1, handle_call/3, handle_cast/2, handle_info/2, terminate/2,
  code_change/3
]).

start() -> gen_server:start(?MODULE, [], []).
sum(Server, A, B) -> gen_server:call(Server, {sum, A, B}).

init(_) -> {ok, undefined}.

handle_call({sum, A, B}, _From, State) -> {reply, A + B, State};
handle_cast(_Msg, State) -> {noreply, State}.
handle_info(_Info, State) -> {noreply, State}.
terminate(_Reason, _State) -> ok.
code_change(_OldVsn, State, _Extra) -> {ok, State}.
{% endhighlight %}

Y el mismo programa en Elixir

{% highlight elixir %}
defmodule SumServer do
  use GenServer

  def start do
    GenServer.start(__MODULE__, nil)
  end

  def sum(server, a, b) do
    GenServer.call(server, {:sum, a, b})
  end

  def handle_call({:sum, a, b}, _from, state) do
    {:reply, a + b, state}
  end
end
{% endhighlight %}

Aún si saber nada de Elixir o Erlang te puedes dar cuenta que Elixir es mucho más legible, es más familiar a lo que estamos acostumbrados. Esto ayuda mucho porque muchas personas se sienten intimidados cuando ven algo como Erlang y sienten que no pueden aprender programación funcional de manera fácil o han escuchado de lo bueno que es la plataforma Erlang pero por la sintaxis del lenguaje no se animan a entrar.

Además Elixir tiene mejores herramientas que vienen integradas como lo es mix, que es una herramienta con la cual puedes crear tus proyectos, compilarlos y ejecutarlos con solo unos pocos comandos

    $ mix new my_project
    $ cd my_project/
    $ mix test

Como puedes ver, Elixir tiene todas las ventajas de Erlang,nos provee una sintaxis más familiar y además agrega varias herramientas que nos ayuda a crear programas de manera más sencilla.

A lo largo de esta serie vamos a ir aprendiendo lo básico de la sintaxis del lenguaje y a utilizar algunas herramientas de Elixir, también empezarás a conocer cómo crear un sitio web con Phoenix, que es el framework que usa Elixir para construir aplicaciones web.

### Referencias

[Sitio oficial de Elixir](https://elixir-lang.org/)

[Elixir in Action](https://www.amazon.com.mx/gp/product/161729201X/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=161729201X&linkCode=as2&tag=rincondev-20&linkId=00b5f89c848a4e0d66627db3e99a118e)

[The Erlangelist](http://theerlangelist.com/)


<!-- Paginator-->
<div class="next-previous clearfix">
  <div class="floater-wrap">
    <div class="toc">
      <a href="/introduccion-a-elixir/" class="toc-icon">
        <i class="fa fa-bars" aria-hidden="true"></i>
      </a>
    </div>
    <div class="clearfix prev-next">
      <div class="half half-left tleft">
        <div class="half-wrap">
          <p class="half-label">ACTUAL</p>
          <p class="current-lesson">Introducción a Elixir, ¿Por qué aprender Elixir?</p>
        </div>
      </div>
      <div class="half half-right tright">
        <div class="half-wrap">
          <p class="half-label">SIGUIENTE</p>
          <p>
            <a href="" class="half-link">
              Tipos básicos
            </a>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>

{% if jekyll.environment == 'production' %}
  {% include disqus_comments.html %}
{% endif %}
