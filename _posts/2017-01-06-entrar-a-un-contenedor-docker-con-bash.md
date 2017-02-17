---
layout: post
title:  "Entrar a un contenedor Docker con bash"
date:   2017-01-06 13:44:24 -0600
categories: devops
featured_image: "/assets/img/devops/docker.png"
featured_video:
---

Aunque no es muy común que se ingrese a un 
contenedor de Docker para hacer cambios (estos se deberían automatizar), 
a veces es necesario ingresar al contenedor 
para ver logs, actualizar o realizar alguna acción.

Hacerlo es muy fácil, solamente debemos saber el 
nombre del contenedor o el ID; si no sabes el nombre o el id, es necesario correr

{% highlight bash %}
docker ps
{% endhighlight %}

Te debe aparecer algo parecido a esto

{% highlight bash %}
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
665b4a1e17b6 webserver:latest /bin/bash ... ... 22/tcp, 80/tcp webserver_rails
{% endhighlight %}

Ahora para entrar debemos escribir

{% highlight bash %}
docker exec -i -t 665b4a1e17b6 /bin/bash #o en su caso poner el nombre
{% endhighlight %}

Y se cambiará tu prompt a lo siguiente

{% highlight bash %}
$ root@665b4a1e17b6:/#
{% endhighlight %}

Lo que significará que ya estás dentro del contenedor de Docker y puedes correr algunos comandos de bash.