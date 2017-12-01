---
layout: page
title: Path para el blog
description: Sobre los temas que hablamos en el blog
permalink: /path-blog/
---

<img src="/assets/img/what-devops.jpg" alt="DevOps Path" class="img-responsive author">

Pienso que cada blog debe tener su temática o su plan sobre qué escribe para
así tener una idea sobre los temas a tratar en este blog.

He decidido crear un pequeño **path** o plan para este blog, que estará enfocado
en aprender sobre DevOps, que es lo que quiero aprender y en lo que me estoy
enfocando.

Veamos esto como una pequeña guía de estudio para llegar a aprender lo que
necesitamos saber como DevOps.

## Conocimientos generales

* Aprender a instalar y configurar Linux
  * Empezaremos con Ubuntu y CentOS
* Configurar nuestro servidor web, servidor DNS y servidor de correos
  * Utilizaremos Nginx, Bind y Postfix
* Aprender vim
* Entender de redes
  * Saber cómo funcionan las IP, gateways, máscaras de red
  * Entender CIDR
  * Entender ARP, ICMP, traceroute
* Aprender a cómo monitorear nuestros sistemas
* Entender cómo funciona los manejadores de paquetes
  * En nuestro caso apt y rpm
* Aprender base de datos
  * Nos enfocaremos en Postgresql
* Aprender el sistema de archivos
  * Comenzando con nuestro archivo local (ext3, ext4)
  * Aprender NFS
  * Vistazo a sistemas de archivos distribuidos como Ceph y Gluster

## Gestión de configuraciones

* Aprender Ansible y terraform
* Practicar creando nuestras recetas y hacer un despliegue a servidores

## Programación

* Aprender como escribir scripts de Bash
  * Debemos saber cómo hacer un parsing de nuestros archivos de log
  * awk y sed debemos dominarlo
  * Automatizar (backups, cronjobs)
* Aprender Python
  * Debemos dominar Python
  * Nos servirá para cuestiones en donde bash no es suficiente
* Aprender C
  * No es necesario dominarlo pero sí entenderlo
  * Mucho software de Linux está escrito en C
* Aprender control de versiones
  * Usaremos Git
* Desarrollar usando HTTP y REST services

## Internet y redes

* Entender cómo funcionan los protocolos más comunes y cómo depurarlos
  * SMTP - Debemos ser capaces de ejecutar una transacción SMPT a mano
  * DNS - Saber cómo trabajar con DNS
  * HTTP - Ser capaz de hacer peticiones HTTP y HTTPS
  * TCP/IP - Lo básico nos va a ayudar mucho
* Encriptación
  * Entender PKI y aprender a usar GPG
  * Entender sobre hashing
* Entender sobre fechas
  * Saber que es UTC
  * Saber cómo funciona NTP
* Aprender LDAP
* Configurar Load Balancing con Nginx
* Aprender sobre cache
* Aprender sobre Redis

## Algunos proyectos en los cuales podemos practicar

* Crear una pequeña aplicación web, usando Postgres. El código lo tenemos que probar
en local o en AWS y hacer el despliegue en producción usando Ansible
* Escribir código que pueda agregar y quitar usuarios y grups en un servidor LDAP
* Construir un sistema que automáticamente detecte si el servidor esté caido y que
empiece el proceso de levantarlo y hacer backups
* Hostear tu propia página y si usas generadores estáticos configurar el CI para
que se actualice automáticamente en producción.

Aunque esto pueda parecer mucho o poco dependiendo de quien lo vea, voy
a estar escribiendo sobre estos temas y lo que vaya aprendiendo.

También escribiré algunas cosas sobre tips y trucos de Linux y macOS para
principiantes ya que también necesitamos momentos de relajación.

Me gustaría saber qué te parece este Path, si le falto o le sobra algo o
si lo ves innecesario, siempre se puede mejorar.

{% if jekyll.environment == 'production' %}
  {% include disqus_comments.html %}
{% endif %}
