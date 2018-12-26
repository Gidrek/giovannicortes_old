---
layout: post
title:  "Ambientes virtuales en Python, ¿qué son y cómo los instalo?"
date:   2017-03-13 18:15:32 -0600
description: Aprende a utilizar los ambientes virtuales y usa varios paquetes Python sin conflicto.
categories: python
featured_image: "/assets/img/python/python_post_02.jpg"
featured_video:
excerpt: En esta entrada aprenderemos a instalar ambientes virtuales para poder programar con distintas versiones de bibliotecas de Python y también diferentes versiones de Python.
---


Cuando desarrollamos en Python es común utilizar diferentes versiones de algunos 
paquetes para cada proyecto, por ejemplo, hay ocasiones en que tenemos un 
proyecto que está usando Django 1.6 y queremos crear uno nuevo con Django 1.8. 
Si instaláramos Django de forma global simplemente sería imposible mantener 
varios proyectos al mismo tiempo y esto nos causaría problemas, pero 
afortunadamente en Python tenemos una herramienta simple pero muy 
potente: <strong>los ambientes virtuales</strong>.

Un ambiente virtual es un espacio independiente de otros ambientes virtuales, 
que nos ayuda a mantener separados los paquetes de otros ambientes virtuales 
y los instalados de forma global. Un ambiente virtual es como una caja en donde 
vas a instalar los paquetes que solo van a ser usados por un proyecto en 
particular, de esa forma se resuelven los conflictos al tener varias 
versiones del mismo paquete.

<img src="https://res.cloudinary.com/gidrek/image/upload/c_scale,w_839/v1435783855/ambientes_virtuales.001_ojo1bv.jpg" alt="Ambientes virtuales" />

<h2>Instalando Virtualenv</h2>

Primero que nada, antes de usar los ambientes virtuales, tenemos que 
instalar <strong>virtualenv</strong>, que es la utilería que 
nos genera los ambientes virtuales.

<blockquote>
  Los comandos aquí presentados funcionan en OS X y Linux
</blockquote>

{% highlight bash %}
sudo pip install virtualenv
{% endhighlight %}

<h2>Creando un ambiente virtual con virtualenv</h2>

Cuando creamos un ambiente virtual, lo que virtuales hace es crear una 
carpeta especial en donde se van a instalar todos los paquetes, de esa forma 
tendrás en un solo lugar todo lo necesario para trabajar. Vamos a suponer que 
quieres empezar un proyecto nuevo

{% highlight bash %}
# Creamos la carpeta de nuestro proyecto y entramos en ella
$ mkdir my_project
$ cd my_project
{% endhighlight %}

Una vez dentro de nuestra carpeta, es hora de crear nuestro ambiente virtual

{% highlight bash %}
$ virtualenv myproject
{% endhighlight %}

Esto nos creará una carpeta con la siguiente estructura

{% highlight bash %}
myproject/
  bin/
  include/
  lib/
{% endhighlight %}

En <strong>bin/</strong> se encuentran todos los archivos que nos 
ayudarán a interactuar con los ambientes virtuales, en <strong>include/</strong> se 
encuentran las cabeceras de C que nos ayudarán a compilar algunos paquetes de Python, y 
por último en <strong>lib/</strong> se encuentra una copia de la versión de Python que 
estamos usando, dentro de ella se encuentra la carpeta <em>myproject/lib/python2.7/
site-packages/</em> donde se instalarán los paquetes de nuestro ambiente virtual. Antes 
de seguir hay que tomar en cuenta lo siguiente:

<ul>
<li>El nombre <strong>myproject</strong> es el nombre del ambiente virtual, puedes 
ponerle el nombre que tu desees</li>
<li>Puedes crear la carpeta del ambiente virtual donde desees</li>
<li>Si instalas un paquete de Python en este paso, lo estarás instalando de manera global
(cosa que no queremos), para instalarlo en el ambiente virtual debes leer la siguiente 
sección</li>
</ul>

<h2>Activar un ambiente virtual</h2>

Activar nuestro ambiente virtual es muy fácil

{% highlight bash %}
source myproject/bin/activate
{% endhighlight %}

De esta forma tendremos activado nuestro ambiente virtual. Si te fijas 
en tu terminal, podrás ver en el prompt el nombre del ambiente virtual

{% highlight bash %}
(myproject)$
{% endhighlight %}

Una vez hecho esto ya puedes empezar a instalar los paquetes de python

<h2>Instalando paquetes en el ambiente virtual</h2>

Instalar paquetes en el ambiente virtual es sencillo, simplemente usamos <strong>pip</strong> 
como siempre para instalarlo, pero antes, debemos checar si hay algún paquete instalado, 
para ello usamos pip freeze para mostrar todos los paquetes instalados, si lo hacemos la 
primera vez, no debería haber ningún paquete instalado.

{% highlight bash %}
(myproject)$ pip freeze
(myproject)$ # No debería haber paquetes instalados
{% endhighlight %}

Ahora instalemos un paquete, en este caso Django

{% highlight bash %}
(myproject)$ pip install django
Collecting django
Downloading Django-1.8-py2.py3-none-any.whl (6.2MB)
100% |################################| 6.2MB 59kB/s
Installing collected packages: django

Successfully installed django-1.8

(myproject)$
{% endhighlight %}

Ahora si hacemos un pip freeze nos debería aparecer django instalado

{% highlight bash %}
(myproject)$ pip freeze
Django==1.8
(myproject)$
{% endhighlight %}

Como puedes ver, tenemos Django 1.8 instalado, al utilizar pip install django , pip nos 
instala la última versión, podemos instalar una versión diferente al indicarle cúal es 
la que queremos, por ejemplo `pip install django==1.6.6` Algo muy potente que tienen los 
ambientes virtuales, es que puedes replicarlos en diferentes máquinas instalando los 
mismos paquetes, de esa forma tendrás el mismo ambiente de trabajo en cada computadora. 
Por ahora solo tenemos instalado un paquete, pero cuando trabajemos con proyectos más 
grandes instalar cada paquete a mano en cada computadora sería algo tedioso, pero 
gracias a <strong>pip freeze</strong> podemos automatizar un poco la instalación de 
paquetes. Lo único que necesitamos es que todos esos paquetes estén en un archivo de 
texto.

{% highlight bash %}
(myproject)$ pip freeze > requirements.txt
{% endhighlight %}

Esto nos creará un archivo llamado <strong>requirements.txt</strong> con todos los 
paquetes instalados en nuestro ambiente virtual, si tienes bajo control de código esta 
carpeta entonces cuando vayas a tu otra computadora o a una máquina virtual o servidor, 
simplemente se descargará la última versión de este documento, si no con copiar y pegar 
el archivo podrás instalar los paquetes. Ya que tengas ese archivo, si quieres instalar 
todos los paquetes debes entrar al ambiente virtual en la otra computadora e instalarlo 
con pip

{% highlight bash %}
(myproject)$ pip install -r requirements.txt
{% endhighlight %}


Si le pasamos -r a pip install entonces instalará todos los paquetes del archivo, de esa 
forma no tendremos que instalarlo uno a uno y nos aseguramos que todos los paquetes se 
instalen en la versión que tienes en tu ambiente virtual.

<h2>Desactivar un ambiente virtual</h2>

Cuando ya no queremos usar el ambiente virtual, simplemente usamos el siguiente comando

{% highlight bash %}
(myproject)$ deactivate
$
{% endhighlight %}

<h2>Eliminar un ambiente virtual</h2>

Eliminar un ambiente virtual es tan sencillo como borrar la carpeta del ambiente 
virtual, en nuestro caso, con eliminar la carpeta <strong>myproject</strong>

<h2>Usando virtualenvwrapper para manejar ambientes virtuales</h2>

Aunque el uso de <strong>virtualenv</strong> es sencillo, a veces se puede convertir en tedioso, principalmente cuando queremos activarlo. Para hacer más llevadero el uso de ambientes virtuales podemos instalar <strong>virtualenvwrapper</strong> en nuestros paquetes globales. Primero tenemos que instalarlo

{% highlight bash %}
sudo pip install virtualenvwrapper
{% endhighlight %}

Ahora necesitamos configurar en dónde se van a guardar todas las carpetas de nuestros 
ambientes virtuales, esto es muy útil para tener centralizado todo y si algo sale mal, 
ya sabemos a donde ir y podemos activarlo o borrarlo manualmente, primero debemos abrir 
el archivo de configuración de nuestra terminal, yo estoy utilizando zsh por lo cual mi 
archivo de configuración será .zshrc y si usas bash entonces será .profile En tu archivo 
de configuración es necesario que escribas lo siguiente:

{% highlight bash %}
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
{% endhighlight %}

Una vez que reinicies el shell, ya estarás configurado para trabajar con ambientes 
virtuales. Para crear un ambiente virtual solamente usamos

{% highlight bash %}
$ mkvirtualenv myproject
(myproject)$
{% endhighlight %}

`workon` también nos sirve para listar todos los ambientes virtuales que tenemos, en caso de que hayamos olvidado el nombre de alguno

{% highlight bash %}
$ workon
myproject
giovannicortes
tutorial
{% endhighlight %}

Para eliminar un ambiente virtual solo escribimos rmvirtualenv

{% highlight bash %}
$ rmvirtualenv myproject
$ workon
giovannicortes
tutorial
{% endhighlight %}

Una cosa maravillosa de <strong>virtualenvwrapper</strong> es que no necesitamos estar 
en una carpeta en particular, ya que todo se instala y borra en la carpeta que 
configuramos en nuestro archivo de shell. Espero que esta pequeña introducción les haya 
ayudado para empezar a trabajar con ambientes virtuales en python, es la mejor manera de 
trabajar y te evitas muchos problemas.
