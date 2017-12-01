---
layout: post
title:  "Desplegar un servidor NFS en Centos 7"
date:   2017-08-18 08:48:00 -0600
description: Desplegar un servidor NFS en Centos 7 y configurarlo para los clientes.
categories: linux
featured_image: "/assets/img/linux/linux_post_01.jpg"
featured_video:
---
 NFS (Network File System) es un protocolo que se utiliza para que tengamos un sistema
 de archivos distribuidos en una red de computadoras. Con esto, muchas máquinas clientes
 pueden conectarse a un servidor de archivos y de esa forma poder compartirlo.

 Debido a que el compartir archivos es muy común en empresas, el NFS se usa mucho
 para que archivos importantes estén accesibles para todos o para quienes queramos
 que tengan acceso.

En esta ocasión desplegaremos un servidor NFS y también configuraremos la parte de cliente
para poder crear archivos remotamente.

## Instalando el servidor NFS

Primero vamos a instalar el servidor de NFS y  configurarlo. Los comandos los corremos como
root.

{% highlight bash %}
[root@server ~]# yum install nfs-utils
{% endhighlight %}

Creamos los directorios en donde se va a compartir la información y darle permisos,
por ahora le daremos permisos 777, luego podemos configurarlos para que escriban ciertos
usuarios o ciertos grupos.

{% highlight bash %}
[root@server ~]# mkdir /data
[root@server ~]# chmod 777 /data
{% endhighlight %}

Debido a que Centos 7 viene con SElinux activado que no deja que NFS comparta archivos,
vamos a activar algunas banderas para que podamos compartir

{% highlight bash %}
[root@server ~]# setsebool -P nfs_export_all_ro=1 nfs_export_all_rw=1
{% endhighlight %}

`nfs_export_all_ro` con esta opción ya podemos empezar a compartir el servicio, pero para que
todos puedan leer y escribir entonces activamos `nfs_export_all_rw`

Ya que hayamos puesto los comandos arriba mencionados, hay que asegurarnos que
efectivamente estén encendidos

{% highlight bash %}
[root@server ~]# getsebool -a | grep nfs_export
nfs_export_all_ro --> on
nfs_export_all_rw --> on
{% endhighlight %}

Necesitamos agregar las reglas de firewall para que que no nos bloquee el acceso desde el cliente.

{% highlight bash %}
[root@server ~]# firewall-cmd --permanent --add-service nfs
[root@server ~]# firewall-cmd --reload
{% endhighlight %}

Una vez que ya tenemos instalado y con el firewall listo, es necesario habilitar los servicios,
si no, no podemos usarlos

{% highlight bash %}
[root@server ~]# systemctl enable rpcbind
[root@server ~]# systemctl enable nfs-server
[root@server ~]# systemctl start rpcbind
[root@server ~]# systemctl start nfs
{% endhighlight %}

Hay que modificar el archivo `/etc/exports`, aquí es donde pondremos todos los directorios
que queremos que estén disponibles para NFS, por lo cual podemos poner desde uno hasta los
necesarios, este archivo es importante ya que aquí definimos algunos permisos y protocolos que
podemos usar.

{% highlight bash %}
[root@server ~]# vi /etc/exports
{% endhighlight %}

Y agregamos la siguiente línea

{% highlight bash %}
/data xxx.xxx.xx.xx/24(rw,sync,no_root_squash)
{% endhighlight %}

Donde

* **/data** -> Es el directorio a compartir
* **xxx.xxx.xx.xx/24** -> Es la ip de la maquina con la mascara de subred
* **rw** -> Son permisos de escritura
* **sync** -> Todos los cambios en el sistema de archivos correspondiente se vacían inmediatamente al disco; Se esperan las respectivas operaciones de escritura
* **no_root_squash** -> De forma predeterminada, cualquier solicitud de archivo realizada por el usuario root en la máquina cliente es tratada como por el usuario nobody en el servidor. Si se selecciona no_root_squash, el root en la máquina cliente tendrá el mismo nivel de acceso a los archivos del sistema como root en el servidor.

Una vez que hayamos hecho nuestros cambios hay que exportarlos

{% highlight bash %}
[root@server ~]# exportfs -avr
{% endhighlight %}

Por último reiniciamos los servicios para que tomen efecto los cambios que hicimos

{% highlight bash %}
[root@server ~]# systemctl restart rpcbind
[root@server ~]# systemctl restart nfs
{% endhighlight %}

Ya con esto tenemos configurado el servidor de NFS ahora falta hacer la parte del
cliente pero eso es más fácil.

## Configurar el cliente NFS

Ya que estemos en el cliente desde donde queremos accesar a nuestro servidor NFS
tenemos que instalar  también las utilidades de NFS

{% highlight bash %}
[root@client ~]# yum install nfs-utils
{% endhighlight %}

Y activar los servicios

{% highlight bash %}
[root@client ~]# systemctl enable rpcbind
[root@client ~]# systemctl start rpcbind
{% endhighlight %}

Hay que crear un directorio que es el que vamos a montar en nuestro servidor

{% highlight bash %}
[root@client ~]# mkdir /mnt/data
{% endhighlight %}

Ya con esto, podemos montar nuestro directorio

{% highlight bash %}
[root@client ~]# mount -t nfs -o rw <ip or host nfs server>:/data /mnt/data
{% endhighlight %}

Vamos a ver si realmente se montó

{% highlight bash %}
[root@client ~]# df -h
<ip or host>:/data                   500G  1.7G  499G   1% /mnt/data
{% endhighlight %}

Si aparece el punto de montaje, ¡felicidades!, ya tienes un servidor NFS listo.

Para no montar el NFS cada vez que reiniciemos la máquina, podemos modificar
`fstab` para que se automonte

{% highlight bash %}
[root@client ~]# vi /etc/fstab
<ip or host server>:/data/  /mnt/data nfs rw,sync,hard,intr 0 0
{% endhighlight %}

Ya con esto tenemos configurado tanto cliente como servidor, como te darás cuenta
aún falta configurar permisos o también generar carpetas para grupos de usuarios, pero
este tutorial te servirá para iniciar tu servidor NFS.

En un siguiente tutorial, vamos a utilizar **kerberos** para agregarle seguridad a nuestra
instalación y que esté mejor protegido nuestros archivos.
