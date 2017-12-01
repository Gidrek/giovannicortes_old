---
layout: post
title:  "Instalar Django con Ubuntu, Ngnix, Postgres y Gunicorn"
date:   2017-08-16 22:33:00 -0600
description: Instala tu aplicación Django en Ubuntu utiizando Postgres, gunicorn y Nginx, listo para producción.
categories: python
featured_image: "/assets/img/python/django_post_01.jpg"
featured_video:
---

# 1. Introducción

Desplegar un sitio en producción es un sufrimiento, incluso servicios que “simplifican” el deployment como
Heroku hacen un poco menos pesado el atrejeo pero aún así sigue siendo un sufrimiento, más en casos
como Django que no es tan sencillo como con PHP y Apache.

Mi intento con este artículo no es solo escribir una guía comprensiva, si no que también me servirá para tener de
referencia rápida de cómo hacerlo. Esta guía lo haré conforme los pasos que hice, por lo tanto talvez no sea
la mejor pero trataré de que sea muy sencilla de seguir.

# 2. Recursos

Como tuve que investigar cómo hacerlo, más que nada esta es un compendio de conocimientos regados por internet,
aquí solamente pondré lo necesario para llevar a producción un sitio hecho en Django.

Muchas de estas páginas son en caso de que quieras adentrarte más luego de haber instalado tu proyectos y
algunos tutoriales que te servirán para tener en claro algunas cosas que a lo mejor no digo aquí, como instalar el servidor Ubuntu.

## 2.1. Docs

* [Django Deployment checklist](https://docs.djangoproject.com/en/1.11/howto/deployment/checklist/)
* [Gunicorn](http://docs.gunicorn.org/en/19.3/)
* [Nginx](http://nginx.org/en/docs/)

## 2.2. Tutoriales

* Linode - [Loguear a un servidor remoto y crear tu hostname](https://www.linode.com/docs/getting-started)
* Linode - [Seguridad básica de tu servidor](https://www.linode.com/docs/security/securing-your-server/)

# 3. Generalidades

Debido a que poner todo exactamente aquí podría hacer de esta guía muy larga, y a lo mejor la
mayoría de ustedes ya lo saben, aquí habrá algunas cosas que asumo que ya conoces.

## 3.1. General

Esta guía asume que tienes un conocmiento básico de Linux y que trabajas con la terminal.
Cosas como ejecutar un comando, listar el contenido de una carpeta,
usar un editor en la terminal ya sea Vim o el que más te guste, además de crear carpetas.
Algunos comandos aquí listados serán más complicados que los que acabo de decir.

## 3.2. Stack

En la guía voy a Utilizar `Ubuntu 16.04 LTS`, aunque igualmente puedes utlizar
cualquier distro de Linux, con los cambios necesarios.

La versión de Python será la 3.6.2. La versión de Django será 1.11 y utilizaremos la versión de Python 3.
El sistema de versión de controles será Git.

El stack para el sitio será la base de datos PosgreSQL, el servidor Nginx,
y como nuestro servidor WSGI será Gunicorn. La base de datos, el servidor,
los archivos estáticos se asumen que serán instalados en el mismo servidor.

## 3.3. Proyecto Django

Asumo que tu proyecto ya fue probado localmente y está listo para instalarlo en producción.
Algunas guías se enfocan a instalar un proyecto inicial, sin nada más que lo que
creas al utilizar el comando para generar el sitio con Django,
otras lo hacen con el famoso tutorial de Polls, en mi caso esta guía
pretende instalar un proyecto, aunque sencillo,
pero tiene algunas cosas que se ven en un proyecto normal.

## 3.4. Layout

El proyecto asume la siguiente estructura.

```
yourprojectrepo/              # REPOSITORY ROOT
requirements.txt
yourproject/              # PROJECT ROOT
    manage.py
    static/               # Archivos estáticos aquí
        css/
        images/
        [etc...]
    yourproject/          # CONFIGURATION ROOT
        __init__.py
        urls.py
        wsgi.py
        settings/         # Nota que es un módulo
            __init__.py
            common.py
            development.py
            production.py
```

No voy a pedir que cambies la estructura de tu proyecto, solamente lo
pongo aquí por pura referencia.

Lo más importante es el PROJECT ROOT, CONFIGURATION ROOT y que te des cuenta que los
settings de nuestro proyecto están como paquetes, de esta forma será más fácil la configuración.

## 3.5. Variables del virtualenv y virtualenvwrapper

La guía asume que usamos virtualen y virtualenvwrapper, tanto en local como en produccón.

Configuración sensible como la secret key, la contraseña de la base de datos, etc,
será guardado en variables de ambiente, las cuales podemos configurar en los archivos
`postactivate` y `predeactivate` que podemos encontrar en la carpeta de
nuestros ambientes virtuales, si has seguido el [tutorial que hice](https://giovannicortes.com/ambientes-virtuales-en-python-que-son-y-como-los-instalo/), debería estar en
`/home/yourname/.virtualenvs`, los archivos que necesitamos están en

```
.virtualenvs/
yourproject/
    bin/
        postactivate
        predeactivate
```

postactivate debería lucir como este

{% highlight bash %}
#!/bin/bash
# Es activado cuando el virtualenv es activado

# Pon tu secret key, esta no es real, solo lo puse de ejemplo
export SECRET_KEY='%gsj1llKksP*//sadH'
export DB_USERNAME=yournamehere
export DB_PASSWORD=dbpassword
{% endhighlight %}

Y el archivo predeactivate debería contener:

{% highlight bash %}
#!/bin/bash
unset SECRET_KEY
unset DB_USERNAME
unset DB_PASSWORD
{% endhighlight %}

Para que Django pueda acceder a estos valores, debemos sustituir los datos en
el archivo de settings, por ejemplo:

{% highlight python %}
import os
SECRET_KEY = os.environ.get('SECRET_KEY')
{% endhighlight %}

# 4. Instalación

Ya que tenemos claro qué vamos a usar y cómo, es hora de ponernos manos a la obra para la instalación de nuestro proyecto, desde cero.

## 4.1. Crea la instancia de tu servidor en Linode

He usado Linode desde hace unos 5 años y nunca me ha dado problemas, actualmente
los precios están muy accesibles y tienen memorias SSD.

Los pasos necesarios los encontrarás en el `Getting Started` de Linode. Si no tienes Linode y
quieres probarlo te agradecería que lo hicieras mediante mi [código de referencia](https://www.linode.com/?r=e3a4081bc1c2d2d1c52725999a9043ac2dff0f7a) :)

Igualmente otro muy bueno es DigitalOcean.

Como referencia, mis settings del servidor son

* Hardware: Intel(R) Xeon(R) CPU E5-2680 2 Cores 2GB RAM
* Región: Dallas
* No extras
* Distribución: Ubuntu Linux 16.04 LTS x64

Si vas a usar Ubuntu con memoria RAM menor a 1 GB, se recomienda la versión de 32 bits.

## 4.2. Compra un dominio (opcional)

Si quieres que sea más fácil encontrar tu sitio web en internet, es recomendable que compres un dominio,
hay varios lugares donde puedes comprarlos, entre ellos están **Godaddy** y **Namecheap**, elige el que más te guste.

### 4.2.1 Configura tus DNS

Una vez que tengas tu domino, la mejor forma de usarlo es configurar los DNS en Linode,
son rápidos y además son efectivos, no me han dado problemas, para hacer esto recomiendo leer
la guía de [Hosting a WebSite](https://linode.com/docs/websites/hosting-a-website/), en donde al partir de **Adding DNS Records**
podrás configurarlos.

## 4.3. Configuración inicial del servidor

En este punto debes configurar tu servidor, de tal formas que tengas actualizado Ubuntu y
crees las llaves SSH, simplemente sigue las instrucciones del [Getting Started](https://linode.com/docs/getting-started) y
también la guía de [Securing Your Server](https://linode.com/docs/security/securing-your-server/).

Con estas dos guías tendrás la configuración básica del servidor.

## 4.4. Instalando cosas

Ya que tienes tu servidor listo, es hora de empezar a instalar lo que vamos a necesitar. Asumimos que
Python 3 es el intérprete utilizado.

Recuerda que estos comandos se harán en la terminal de tu servidor Linux.

### 4.4.1. pip

{% highlight bash %}
sudo apt-get install python-setuptools
sudo easy_install pip
{% endhighlight %}

### 4.4.2. PostgreSQL

{% highlight bash %}
sudo apt-get install libpq-dev python3-dev
sudo apt-get install postgresql postgresql-contrib
{% endhighlight %}

### 4.4.3. Nginx

{% highlight bash %}
sudo apt-get install nginx
{% endhighlight %}


### 4.4.4 git

{% highlight bash %}
sudo apt-get install git
{% endhighlight %}

### 4.4.5 virtualenv

{% highlight bash %}
sudo pip install virtualenv
{% endhighlight %}

### 4.4.6. virtualenvwrapper

{% highlight bash %}
sudo pip install virtualenvwrapper
{% endhighlight %}

Luego de haberlo instalado, configura tu archivo `.zshrc` o `.bashrc`

{% highlight bash %}
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
{% endhighlight %}

### 4.5. Configurar PostgreSQL

Ahora es tiempo de configurar la base de datos. Primero, necesitamos cambiar al usuario postgres

{% highlight bash %}
sudo su - postgres
{% endhighlight %}

Hay que crear la base de datos. Recuerda que el nombre de la base de datos es el mismo que el
que tienes en tus settings.

{% highlight bash %}
createdb yourproject
{% endhighlight %}

Creamos un nuevo usuario, éste debe coincidir con los settings de tu base de datos, al crear el usuario te
pedirá contraseña, que es la que vas a utilizar en tu base de datos.

{% highlight bash %}
createuser -P yournamehere
{% endhighlight %}

Vamos a entrar al shell de PostgreSQL y dar los privilegios al usuario

{% highlight bash %}
psql
GRANT ALL PRIVILEGES ON DATABASE yourproject TO yournamehere;
\q
{% endhighlight %}

Finalmente, regresa a tu usuario normal

{% highlight bash %}
su - yournamehere
{% endhighlight %}

## 4.6. Crear un ambiente virtual

Lo siguiente será crear nuestro ambiente virtual para trabajar con Python 3

{% highlight bash %}
mkvirtualenv yourvirtualenvname -p /usr/bin/python3
{% endhighlight %}

Ahora es tiempo de poner las variables de los ambientes virtuales como mencioné arriba.

## 4.7. Clonar el proyecto

Es hora de clonar nuestro proyecto, crearemos un directorio en nuestra carpeta `home, para que encontremos
más fácil nuestros paths.

En tu terminal escribe

{% highlight bash %}
cd
mkdir web
cd web/
git clone https://github.com/yournamehere/yourprojectrepo.git
{% endhighlight %}

Reemplazando la url con la tuya.

## 4.8. Instalando los requerimientos de tu proyecto

Tendremos que instalar los requerimientos de nuestro proyecto. Si tienes el archivo `requirements.txt` (que deberías), es muy fácil
instalarlos. Asegúrate de estar en el ambiente virtual de tu proyecto

### 4.8.1. Activa el ambiente virtual

{% highlight bash %}
workon yourproyect
{% endhighlight %}


### 4.8.2. Instala los requerimientos

{% highlight bash %}
cd yourproject
pip install -r requirements.txt
{% endhighlight %}

### 4.8.3. Instalar Gunicorn

Si gunicorn no está en los requerimientos, tenemos que instalarlo

{% highlight bash %}
pip install gunicorn
{% endhighlight %}

### 4.8.4. Migrate

Si ya tienes la configuración de tu base de datos y además has hecho tus pruebas en local, es hora de sincronizarlo con Django.

{% highlight bash %}
# En el root de tu proyecto
python manage.py migrate
{% endhighlight %}

### 4.8.5. Crear el superusuario de Django

En caso de que lo necesites, crea el superusuario

{% highlight bash %}
# En el root de tu proyecto
python manage.py createsuperuser
python manage.py migrate
{% endhighlight %}

Sigue las instrucciones que te van apareciendo.

### 4.8.6. Collecstatic

Cuando hacemos un collectstatic, Django copia todos los archivos en la carpeta de
static que tenemos configurado y los copia al STATIC_ROOT, estos son los archivos estáticos que usará Nginx.

{% highlight bash %}
# En el root de tu proyecto
python manage.py collectstatic --settings=yourproject.settings.production
{% endhighlight %}

Contesta que si, y Django se encargará del resto

## 4.9. Configurar Nginx

Esta sección asume que tenemos configurado Django de la siguente forma

{% highlight python %}
STATIC_ROOT = os.path.join(BASE_DIR, 'public', 'static')
STATIC_URL = '/static/'
{% endhighlight %}

`STATIC_ROOT` es el directorio en donde se copiaron los archivos estáticos, de acuerdo a
la configuración será en `/home/yourname/web/yourproject/yourproject/public/static/`

Para configurar Nginx, un bloque debe ser creado. La meta de la configuración de
Nginx será servir todos los archivos estáticos con Nginx y dejar
los otros request a Gunicorn (y Django)

Crea un nuevo archivo de configuración

{% highlight bash %}
sudo vim /etc/nginx/sites-available/yourproject
{% endhighlight %}

El nombre del archivo no importa, pero es mejor tener consistencia con el nombre de tu proyecto.
Dentro del archivo, escribe lo siguiente

    server {
      listen 80;
      server_name yourproject;

      location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      }

      location /static/ {
        alias /home/yourname/web/yourproject/yourproject/public/static/;
      }
    }

Has las sustituciones si es necesario:

* Después de server_name, usa el nombre de tu proyecto o dominio
* El path en alias debe coincidir con el path de tus archivos estáticos generados con `collectstatic`, para estar seguros, ve hacia el directorio de tus archivos estáticos y escribe dentro de la carpeta static `pwd`, copia y pega el path
* El puerto en `proxy_pass` debe ser el puerto que intentas usar con Gunicorn, no cambies la IP
* El path después de location debe coincidir con tu `STATIC_URL de tu configuración

Es hora de activar nuestro sitio, debemos poner un enlace simbólico a la carpeta de sites-enabled

{% highlight bash %}
cd /etc/nginx/sites-enabled
sudo ln -s ../sites-available/yourproject
{% endhighlight %}

Elimina el archivo default que no vamos a necesitar

{% highlight bash %}
sudo rm default
{% endhighlight %}

Finalmente, es hora de activar nuestro nuevo sitio

{% highlight bash %}
sudo service nginx restart
{% endhighlight %}

## 4.10. Iniciar Gunicorn

Una vez que tenemos configurado Ngnix, es hora de inciar Gunicorn, nuestro servidor HTTP de producción.

En la terminal, ten activado tu ambiente virtual

{% highlight bash %}
# Corre este comando en el root de tu proyecto
gunicorn yourproject.wsgi --bind 127.0.0.1:8000
{% endhighlight %}

Fíjate que el puerto sea el mismo que configuraste en Ngnix.

Ahora ve a tu dominio o a la IP de tu sitio, y tu sitio, esperemos, aparecerá. Si tu sitio aparece ¡felicidades!,
prácticamente tienes todo listo. Mata Gunicorn con `CTRL-C` para prepararte para crear el daemon y que Gunicorn trabaje en el background.

Primero, es necesario que hagamos una carpeta donde podamos ver los logs de Gunicorn por si algo sale mal.
Lo haré en la carpeta web, afuera del directorio del repositorio.

{% highlight bash %}
cd
cd web
mkdir logs
touch logs/yourproject_gunicorn.log
cd yourprojectrepo/yourproject
{% endhighlight %}


Ahora es tiempo de iniciar gunicorn como un demonio

{% highlight bash %}
gunicorn yourproject.wsgi --bind 127.0.0.1:8000 --daemon --log-file ~/dev/logs/yourproject_gunicorn.log --workers=3
{% endhighlight %}

La documentación de gunicorn recomienda poner los workers en *“in the 2-4 x $(NUM_CORES) range.”*

Puedes ponerlo en un script para reutilizarlo.

# 5. Mantenimiento

Algunos consejos útiles para mantener tu servidor corriendo

## 5.1. Parar gunicorn

Reiniciar gunicorn puede ser necesario luego de hacer un pull de tu repositorio. Si quieres pararlo simplemente usa

{% highlight bash %}
pkill gunicorn
{% endhighlight %}

Esto matará el proceso llamado gunicorn

## 5.2. collectstatic

Siempre que hagas algún cambio en los estáticos de tus sitios, debes correr el collectstatic para que puedan ser vistos los nuevos cambios.

# 6. Conclusión

En este punto, tu sitio debería estar en el servidor de producción. Espero que esta guía te haya ayudado un poco a hacer el deployment
 de tu aplicación, ya que es uno de los puntos más engorrosos cuando trabajamos con Django.

Si tienes algún problema recomiendo que vayas paso a paso, y que te fijes bien en la configuración que doy y lo compares con l
a que tienes. Si aún tienes problemas, puedes escribir y con gusto te ayudaré en lo que pueda.

Si ves algún error en la guía, me gustaría que lo dijeras para irlo mejorando. Espero dentro de poco sacar un video con
los mismos pasos para que sea un poco más visual.

