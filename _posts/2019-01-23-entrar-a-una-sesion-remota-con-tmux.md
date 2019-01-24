---
layout: post
title:  "Entrar a una sesión remota con tmux"
date:   2019-01-23 19:44:24 -0600
description: tmux es una herramienta muy importante para administrar servidores.
categories: linux
featured_image: "/assets/img/linux/tmux-panes.png"
featured_video:
---

Hace poco tuve la necesidad de usar Tmux en un servidor remoto para hacer algunas cosas de administración, y no 
conocía como usarlo, luego de un poco de investigación aprendí lo básico y aquí algunos comandos para poder usar 
Tmux si tienen necesidad de hacerlo algún día.

### Entrar a una sesión remota

{% highlight bash %}
ssh <username>@<hostname> -t tmux a
{% endhighlight %}

### Salir de una sesión remota

{% highlight bash %}
Ctrl + B, D
{% endhighlight %}

### Crear una nueva ventana de Tmux

{% highlight bash %}
Ctrl + B, C
{% endhighlight %}

### Renombrar una ventana o ponerle nombre

{% highlight bash %}
Ctrl + B, ,
{% endhighlight %}

### Moverse a una ventana

{% highlight bash %}
Ctrl + B, [número de la ventana]
{% endhighlight %}

Con esos comandos básicos ya se puede uno empezar a usar Tmux, próximamente espero poder usarlo más y aprender y 
porque me está gustando cómo se maneja.