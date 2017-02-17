---
layout: post
title:  "Instalar Python 2 y Python 3 en macOS"
date:   2017-02-16 08:46:55 -0600
categories: python
featured_image: "/assets/img/python/python-logo.jpg"
featured_video:
---

Una de las cosas que pasa cuando estas trabajando en macOS es que por 
defecto tienes Python 2 y a veces es un caos instalar Python 3 y 
trabajar en partes separadas los proyectos, lo bueno es que en Python existen 
los ambientes virtuales por lo cual no tendrás problemas cuando quieras 
trabajar algunos proyectos con Python 2 y otros con Python 3, 
así podrás experimentar lo que desees.

Para hacer esto vamos a necesitar configurar un poco nuestro sistema,
pero no es tan difícil y una vez hecho ya podrás estar jugando con tu
versión de Python favorito.

## Instalar pip y virtualenv

Primero que nada vamos a necesitar tener instalado nuestros ambientes
virtuales, si estás empezando con una instalación limpia es necesario
instalar pip.

Instalar pip sudo `easy_install pip`
Instalar virtualenv `sudo pip install virtualenv`
Instalar virtualenvwrapper `sudo pip install virtualenvwrapper`
Configurar tus ambientes virtuales en tu archivo de configuración de la terminal
Yo estoy utilizando zsh por lo cual mi archivo de configuración será .zshrc y si usas bash entonces será .profile

En tu archivo de configuración es necesario que escribas lo siguiente:

{% highlight bash %}
export WORKON_HOME=$HOME/.virtualenvs source /usr/local/bin/virtualenvwrapper.sh
{% endhighlight %}

De esta forma ya tendremos configurado nuestro virtualenwrapper para
trabajar con ambientes virtuales.

## Instalar Homebrew

Homebrew es una excelente herramienta que debería venir por defecto en 
macOS. Si has usado Linux, por ejemplo Ubuntu, te habrás dado cuenta que 
tiene un manejador de paquetes, en el caso de Ubuntu es apt por lo 
cual con un simple apt-get install  puedes instalar muchas cosas,
lo mismo con homebrew. Para usar Homebrew podemos instalarlo de la siguiente forma:

{% highlight bash %}
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"sh
{% endhighlight %}

Al terminar no olvides darle `brew doctor` antes de instalar cualquier cosa.

## Instalar Python 3

Ya que tenemos instalado Homebrew es hora de instalar Python 3

{% highlight bash %}
brew install python3
{% endhighlight %}

Espera a que termine y una vez hecho esto puedes checar si se instaló 
bien al ver la versión de Python 3 que tienes

{% highlight bash %}
python3 -V
{% endhighlight %}

# Crear un ambiente virtual con Python 3

Ya tenemos todo listo, ahora es tiempo de crear nuestro ambiente virtual con Python 3

{% highlight bash %}
mkvirtualenv python3Test -p /usr/local/bin/python3
{% endhighlight %}

**python3Test** es el nombre del ambiente virtual, tu puedes ponerle el 
nombre que te guste, ahora simplemente puedes trabajar con tu 
ambiente virtual de Python 3 siempre que quieras con `workon python3Test`

Una vez que estés dentro de tu ambiente virtual puedes checar la 
versión de Python para que veas con cual estás trabajando

{% highlight bash %}
python -V
{% endhighlight %}

Y te saldrá algo parecido a

{% highlight python %}
Python 3.6.0
{% endhighlight %}

Como puedes ver, ya no tuvimos que usar `python3 -V`  para ver la versión 
ya que por defecto está trabajando con Python 3, ahora simplemente 
puedes instalar cualquier biblioteca que esté disponible en los repositorios 
de Python 3 con `pip install`

Eso es todo, ahora estás listo para trabajar con Python 2 o 
Python 3 creando sus ambientes virtuales.

