---
layout: default
title: Conociendo la shell- Iniciando con Linux
description: Aprende los comandos básicos para manejar la terminal
permalink: /empieza-linux/conociendo-la-shell/
---
# Conociendo la Shell

<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/o8Yw2_DYdyo"></iframe>
</div>

## Algunos comandos sencillos de Linux

Mostrar archivos

{% highlight bash %}
ls
ls -a
ll
ls -ls
ls <nombre path del directorio>
{% endhighlight %}

Movernos entre directorios

{% highlight bash %}
cd <nombre o path del directorio>
{% endhighlight %}

Mostrar el contenido de un archivo

{% highlight bash %}
cat <archivo>
{% endhighlight %}

Crear un archivo

{% highlight bash %}
touch <nombre del archivo>
{% endhighlight %}

Crear un directorio

{% highlight bash %}
mkdir <nombre del directorio>
{% endhighlight %}

Borrar un archivo

{% highlight bash %}
rm <nombre o path del archivo>
{% endhighlight %}

Borrar un directorio

{% highlight bash %}
rmdir <nombre del directorio> # Si el directorio está vacío
rm -rf <nombre del directorio>
{% endhighlight %}

Mostrar las variables de ambiente

{% highlight bash %}
env
{% endhighlight %}

Imprimir en pantalla

{% highlight bash %}
echo <variable o cadena>
{% endhighlight %}

Usar less

{% highlight bash %}
less <nombre del archivo>
{% endhighlight %}

Mostrar ayuda e información


{% highlight bash %}
man <comando>
<comando> --help
{% endhighlight %}

<!-- Paginator-->
<div class="next-previous clearfix">
  <div class="floater-wrap">
    <div class="toc">
      <a href="/empieza-linux/" class="toc-icon">
        <i class="fa fa-bars" aria-hidden="true"></i>
      </a>
    </div>
    <div class="clearfix prev-next">
      <div class="half half-left tleft">
        <div class="half-wrap">
          <p class="half-label">ACTUAL</p>
          <p class="current-lesson">Conociendo la Shell</p>
        </div>
      </div>
      <div class="half half-right tright">
        <div class="half-wrap">
          <p class="half-label">SIGUIENTE</p>
          <p>
            <a href="/empieza-linux/navegando-en-nuestro-servidor/" class="half-link">
              Navegando en nuestro servidor
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
