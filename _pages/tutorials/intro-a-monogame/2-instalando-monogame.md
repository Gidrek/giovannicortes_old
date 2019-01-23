---
layout: tutorials
title: Instalando MonoGame
description: En este tutorial te enseño las herramientas que vas a necesitar para programar con MonoGame
permalink: /2-instalando-monogame/
---

Antes de empezar con MonoGame, es necesario instalar algunas herramientas que nos van a ayudar
a crear nuestro videojuego. Afortunadamente, ¡todas son gratis!

En especial, necesitamos dos herramientas

* Visual Studio
* MonoGame

## Instalar Visual Studio

Lo primero que debemos instalar es Visual Studio. Visual Studio es un IDE que está hecho por Microsoft y
que nos permite escribir en código C# (y otros lenguajes).

Existen dos versiones de Visual Studio, Visual Studio para Windows y Visual Studio para Mac, además, cada
versión viene en tres diferentes opciones: Community, Professional y Enterprise.

La versión que vamos a utilizar es la **Community** ya que es gratis y nos sirve para nuestros propósitos.
No es necesario que gastemos dinero en otras versiones si no le vamos a sacar el provecho y para aprender,
con Community tenemos más que de sobra.

### Descargar Visual Studio

Para descargar Vistual Studio, debemos dirigirnos a [https://visualstudio.microsoft.com/](https://visualstudio.microsoft.com/)
y elegir **Visual Studio IDE**. Hay otra herramienta que se llama **Visual Studio Code** pero por ahora no la usaremos
ya que aplicaríamos otros pasos para configurarlo.

Elige la versión Community de tu Sistema Operativo

![Visual Studio IDE](/assets/img/tutorials/intro-monogame/install-visualstudio.png "Visual Studio IDE")

Una vez descargado, da doble click y sigue las instrucciones para instalarlo. Por lo general es Siguiente, Siguiente.

### Instalar MonoGame

Una vez que ya tengamos Visual Studio instalado, es necesario instalar MonoGame. La versión actual de MonoGame cuando
estoy escribiendo esto (17 de enero de 2019) es la **3.7.1**, pero si es necesario, descarga la versión más actual.

Para descargar MonoGame ve a [http://www.monogame.net/downloads/](http://www.monogame.net/downloads/) y sigue el link de
la versión más actual. Descarga el MonoGame para tu sistema operativo.

## MonoGame en Linux

Dado que MonoGame es multiplataforma, también puedes programar videojuegos en Linux. En Linux no hay Visual Studio, pero lo
más parecido es MonoDevelop, que es el IDE del cual se derivó Visual Studio for Mac, por lo cual encontrarás muchas similitudes
y podrás seguir los tutoriales de forma más sencilla.

### Descargar MonoDevelop

Para descargar MonoDevelop debes ir a la siguiente dirección [https://www.monodevelop.com/download/#fndtn-download-lin](https://www.monodevelop.com/download/#fndtn-download-lin)
y seguir las instrucciones para tu distribución. 

### Instalando MonoGame en Linux

Simplemente descarga y corre el instalador


    wget http://www.monogame.net/releases/v3.6/monogame-sdk.run
    chmod +x monogame-sdk.run
    sudo ./monogame-sdk.run

El instalador automáticamente checará y te avisará si hay alguna dependencia que falta.


## Resumiendo

Ya en este punto tendrás todo lo necesario para empezar a hacer juegos con MonoGame.

> Nota: En los tutoriales diré Visual Studio, pero será un placeholder ya sea para Visual Studio para Windows, Visual Studio para Mac o MonoDevelop

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
          <p class="current-lesson">Instalando MonoGame</p>
        </div>
      </div>
      <div class="half half-right tright">
        <div class="half-wrap">
          <p class="half-label">SIGUIENTE</p>
          <p>
            <a href="/3-disenando-nuestro-juego/" class="half-link">
              Diseño de nuestro juego
            </a>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>