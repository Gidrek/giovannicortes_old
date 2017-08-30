---
layout: default
title: Creando nuestro laboratorio - Iniciando con Linux
description: Creamos un laboratorio para trabajar con servidores
permalink: /empieza-linux/nuestro-laboratorio/
---

# Creando nuestro laboratorio

<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="https://www.youtube.com/embed/ouDUVCCAndc"></iframe>
</div>

## VirtualBox

Usaremos vagrant para crear nuestro laboratorio en local, por
lo cual tendremos que instalar [VirtualBox](https://www.virtualbox.org/)

## Vagrant

[Vagrant](https://www.vagrantup.com/) nos servirá para que sea más fácil instalar Ubuntu

Hay que ir al escritorio (o donde quieras tener tu máquina virtual)

{% highlight bash %}
cd ~/Desktop
{% endhighlight %}

Crear la carpeta y entrar

{% highlight bash %}
mkdir Ubuntu
cd Ubuntu
{% endhighlight %}

Crear el archivo de `Vagrantfile`

{% highlight bash %}
vagrant init
{% endhighlight %}

Editar el archivo de vagrant para instalar Ubuntu

{% highlight ruby %}
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
end
{% endhighlight %}

Levantar vagrant y entrar al vagrant

{% highlight bash %}
vagrant up
vagrant ssh
{% endhighlight %}

## Linode

Puedes usar [Linode](https://www.linode.com/?r=e3a4081bc1c2d2d1c52725999a9043ac2dff0f7a) (tiene un link de referencia) o DigitalOcean para crear tu
servidor.

Una vez que tengamos listo nuestro servidor, procedemos a ingresar
como root

{% highlight bash %}
ssh root@tu-direccion-ip
{% endhighlight %}

Dentro del servidor procedemos a actualizar

{% highlight bash %}
apt-get update
apt-get upgrade
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
          <p class="current-lesson">Creando nuestro laboratorio</p>
        </div>
      </div>
      <div class="half half-right tright">
        <div class="half-wrap">
          <p class="half-label">SIGUIENTE</p>
          <p>
            <a href="/empieza-linux/conociendo-la-shell/" class="half-link">
              Conociendo la Shell
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
