---
layout: post
title:  "Directorios Linux explicados"
date:   2017-02-17 12:27:34 -0600
description: Conoce para qué sirven los directorios de Linux y cómo puedes sacarles provecho.
categories: linux
featured_image: "/assets/img/linux/linux-folders.jpg"
featured_video:
---

Si has usado Linux, te habrás preguntado por qué las carpetas del sistema 
tienen esos nombres, o qué vas a encontrar cuando entras en una carpeta en 
particular. En general, las distribuciones de Linux siguen algo que se llama 
Estándar de Jerarquía de Sistema de Archivos (FHS, en inglés Filesystem 
Hierarchy Standard), que se diseñó para que todas las distribuciones de Linux 
siguieran un orden en particular y así los desarrolladores pudieran saber con 
precisión en qué directorios instalar sus utilidades.

En este artículo vamos a ver cuáles son los directorios y qué contiene 
para que puedas encontrar los archivos que necesites de una forma más rápida.

![Linux FileSystem]({{ site.url }}/assets/img/linux/linux-filesystem.png)

## / – Root

* Todo archivo o directorio empieza desde el root
* Solo el usuario Root tiene privilegios de escritura en este directorio
* No es lo mismo /root que /, el primero es el home del usuario root, el segundo es la raíz de todos los archivos

## /bin – Binarios

* Contiene los binarios ejecutables
* Los comandos esenciales como ls o cp generalmente están en esta carpeta
* Pueden ser usados por todos los usuarios del sistema

## /sbin – Binarios del sistema

* Igual que /bin, tiene binarios que son comunes al sistema Linux
* Generalmente son binarios que pertenecen a tareas administrativas
* No todos los usuarios tienen acceso a los binarios de esta carpeta
* Piensa en que son usados por administradores del sistema, por ejemplo: iptables, reboot, fdisk

## /etc – Archivos de configuración

* Contiene archivos de configuración requerido por los programas
* También tiene archivos para iniciar o detener programas individuales
* Por ejemplo, /etc/resolv.conf

## /dev – Archivos de dispositivos

* Contiene los archivos de los dispositivos instalados
* También incluye terminales, USB o cualquier dispositivo que esté en el sistema
* Ejemplo: /dev/usbone

## /proc – Información de procesos

* Contiene información sobre los procesos de Linux
* Contiene un pseudo sistema de archivos, que consiste en /proc/{id_proceso}, el cual contiene información sobre ese proceso en particular

## /var – Archivos varios

* Contiene información variable
* Información que se espera que crezca con el tiempo, es encontrado aquí
* Archivos de logs (/var/log), paquetes y base de datos (/var/lib), email (/var/mail), archivos temporales (/var/tmp)

## /tmp – Archivos temporales

* Lugar donde se encuentran archivos temporales creados por programas y los usuarios
* Se eliminan en cada reinicio del sistema

## /usr – Rutinas especiales de Linux

* Aunque el nombre puede dar a confusión, no tiene nada que ver con archivos de usuarios
* La jerarquía parece como la de root (/)
* Contiene binarios, archivos, documentación y recursos de segundo nivel
* /usr/bin contiene también binarios, pero son menos usados que los de /bin, por ejemplo: awk, less, scp
* /usr/sbin también contiene binarios del sistema que son menos utilizados, cron, sshd, useradd
* /usr/local contiene programas que se instalan directamente desde el código fuente

## /home – Directorio de inicio

* Es el directorio de inicio de cada usuario
* Este directorio contiene una carpeta por cada usuario en el sistema que puede iniciar sesión
* Solo los usuarios que pertenecen a sus propias carpetas tienen permisos de escritura, lectura y ejecución (además de root)

## /boot – Archivos de carga

* Contiene los archivos de inicio del kernel
* Por ejemplo, grub está localizado en este directorio
* No encontrarás información de cómo Linux bootea en este directorio

# /lib – Bibliotecas del sistema

* Contiene las bibliotecas que son necesarias para los programas de /bin /sbin
* Generalmente son archivos que contienen ld* o li*.*so.* en su nombre
* Por ejemplo: ld-2.11.1.so, libncurses.so.5.7


## /opt – Archivos y binarios opcionales

* Contiene software de terceros
* Muchos sistemas no utilizan este directorio

##  /mnt – Directorio de carga
* Ideado para que los administradores monten otros sistemas de archivos, como USB, otros Discos Duros

## /media – Dispositivos removibles

* Sirve para montar dispositivos removibles como CD-ROM, Floppy Disk, cintas, etc
* Actualmente muy en desuso, se suele utilizar /mnt

Esos son las opciones principales, igualmente tal vez utilices una 
distribución que no siga el estándar al pie de la letra, 
por ejemplo, /bin suele ser un link de /usr/bin por lo cual 
encontrarás todos los binarios que los usuarios pueden utilizar en 
esta carpeta, pero por lo general estos son los usos para los que
están ideados.  ¿Ya conocías el sistema de archivo de Linux?