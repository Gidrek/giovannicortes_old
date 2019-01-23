---
layout: tutorials
title: Diseñando nuestro juego
description: En este tutorial vamos a ver de qué trata el juego que vamos a crear con MonoGame.
permalink: /3-disenando-nuestro-juego/
---

Antes de que empecemos a crear nuestro proyecto, es necesario diseñar nuestro videojuego para
saber qué es lo que vamos a hacer.

## Un simple shooter

Vamos a construir un shooter. Considero que este género es ideal para aprender lo básico de
MonoGame y también tener un juego que podemos mostrar. Al crear este shooter vamos
a poder crear un juego completo y a partir de ahí aprender más sobre MonoGame.

## Diseño del juego

Nuestro shooter va a tener las siguientes características

* El jugador será una nave
* El jugador podrá moverse en la pantalla de derecha a izquierda solamente
* El jugador tendrá tres vidas.
* Habrá un sistema de puntaje
* Habrá tres tipos de enemigos
* Cada enemigo tendrá una diferente vida y  dará diferente puntaje
* Cuando el jugador sea alcanzado por un proyectil enemigo o sea tocado, este perderá una vida
* Habrá una pantalla de Top
* Habra una pantalla de inicio, donde podrá escoger entre iniciar una nueva partida o ver el Top
* El juego será infinito hasta que el jugador pierda las tres vidas

Con esto básico podemos empezar, si se nos ocurre algo más en el camino podemos ir aumentándo
nuestro juego.

## Gráficos

Para nuestro juego vamos a utilizar assets públicos. En particular usaremos este
[https://ansimuz.itch.io/spaceship-shooter-environment](https://ansimuz.itch.io/spaceship-shooter-environment)

Puedes donar si deseas, pero también lo puedes descargar gratis. Como te darás cuenta, mucho de nuestro
diseño del juego viene de este asset, de esa forma podemos limitarnos a tener la mecánica y el arte.

![Shooter](/assets/img/tutorials/intro-monogame/YqY4nK.png "Shooter")

Si notas, los assets que se descargan están pequeños a mi parecer. Así que los edité un poco y les doblé el
tamaño, si deseas los nuevos assets con el nuevo tamaño puedes descargarlo aquí
[https://www.dropbox.com/s/l5h1t80l1mtpcbq/assets_shooter_tutorial.zip?dl=0](https://www.dropbox.com/s/l5h1t80l1mtpcbq/assets_shooter_tutorial.zip?dl=0)

Recomiendo esta descarga, ya que contiene un nuevo sprite del jugador que vamos a utilizar, pero si no, puedes editar
los assets originales.

## Resumiendo

Con el diseño de nuestro juego y con el arte, ya podremos empezar a programar nuestro videojuego. A partir de
ahora vamos a meternos de lleno con MonoGame para aprender.

<!-- Paginator-->
<div class="next-previous clearfix">
  <div class="floater-wrap">
    <div class="toc">
      <a href="/introduccion-a-monogame/" class="toc-icon">
        <i class="fa fa-bars" aria-hidden="true"></i>
      </a>
    </div>
    <div class="clearfix prev-next">
      <div class="half half-left tleft">
        <div class="half-wrap">
          <p class="half-label">ACTUAL</p>
          <p class="current-lesson">Diseñando nuestro juego</p>
        </div>
      </div>
      <div class="half half-right tright">
        <div class="half-wrap">
          <p class="half-label">SIGUIENTE</p>
          <p>
            <a href="#" class="half-link">
              Creando nuestro proyecto
            </a>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>