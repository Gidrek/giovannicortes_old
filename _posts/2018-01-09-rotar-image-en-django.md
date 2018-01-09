---
layout: post
title:  "Rotar imagen en Django cuando se guarda en un modelo"
date:   2018-01-09 13:11:00 -0600
description: Arreglamos el bug cuando la imagen sale rotada cuando se guarda en un image field.
categories: python
featured_image: "/assets/img/python/django_post_02.jpg"
featured_video:
---

Existe un bug con los navegadores que cuando uno toma una foto con el celular de forma frontal
y quiere subirlo a un formulario, por ejemplo para tu perfil, la imagen sale volteada

<p class="center-image">
  <img src="/assets/img/python/posts/imagen-volteada.png" alt="" class="image-post">
</p>

Estuve investigando un poco y es más que nada porque existe información en las imágenes que hace que
los navegadores no lo muestran correctamente, un poco más de información pueden encontrarlo en un
hilo de [Confluence](https://jira.atlassian.com/browse/CONFSERVER-17621). Así que hay que arreglarlo en
el lado del servidor para que podamos ver la imagen correctamente.

Antes que nada, voy a suponer que estamos usando `Pillow` como la biblioteca de imágen, ya que es la
más usada.

## Arreglando el bug

Primero, lo que vamos a hacer es crear una pequeña función que nos ayude a rotar la imagen con ayuda de
Pillow.

{% highlight python %}
from PIL import Image, ExifTags

def rotate_image(filepath):
  try:
    image = Image.open(filepath)
    for orientation in ExifTags.TAGS.keys():
      if ExifTags.TAGS[orientation] == 'Orientation':
            break
    exif = dict(image._getexif().items())

    if exif[orientation] == 3:
        image = image.rotate(180, expand=True)
    elif exif[orientation] == 6:
        image = image.rotate(270, expand=True)
    elif exif[orientation] == 8:
        image = image.rotate(90, expand=True)
    image.save(filepath)
    image.close()
  except (AttributeError, KeyError, IndexError):
    # cases: image don't have getexif
    pass
{% endhighlight %}

> Si quieres saber por qué esos números, aquí más y mejor información sobre la 
  orientación de EXIF [https://www.impulseadventure.com/photo/exif-orientation.html](https://www.impulseadventure.com/photo/exif-orientation.html)

Esa pequeña función nos puede servir para otros proyectos que estemos haciendo, pero en el
caso de Django, necesitamos hacer algunas cosas más para aplicarlo cuando se guarda la imagen.

En este ejemplo, vamos a tener un modelo llamado `Profile` que es donde guardaremos nuestra imagen

{% highlight python %}
from django.db import models
from utilities.utils import get_filename, rotate_image

class Profile(models.Model):
    user = models.ForeignKey(User)
    avatar = models.ImageField(
        verbose_name="Avatar",
        upload_to=get_upload_path,
        blank=True,
        null=True
    )
    ...
{% endhighlight %}

Una vez que tengamos nuestro modelo necesitamos una forma de usar nuestra función para que
procese la imagen una vez que se haya guardado la imagen, para así traer el path como se guardó
y no tener errores.

Para eso, vamos a hacer uso de los `signals` que tiene django, en especial `post_save`, ya que
tenemos que procesar la imagen una vez que se haya guardado en el sistema de archivos.

Hay que modificar nuestro archivo de `models.py` para traer las bibliotecas necesarias

{% highlight python %}
import os

from django.db import models
from django.db.models.signals import post_save
from django.dispatch import receiver

from utilities.utils import get_filename, rotate_image

class Profile(models.Model):
    user = models.ForeignKey(User)
    avatar = models.ImageField(
        verbose_name="Avatar",
        upload_to=get_upload_path,
        blank=True,
        null=True
    )
    ...

@receiver(post_save, sender=Profile, dispatch_uid="update_image_profile")
def update_image(sender, instance, **kwargs):
  if instance.image:
    BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
    fullpath = BASE_DIR + instance.image.url
    rotate_image(fullpath)
{% endhighlight %}

Y ya con este cambio, cuando guardemos una imagen en Django podremos ver la imagen
correctamente

<p class="center-image">
  <img src="/assets/img/python/posts/imagen-normal.png" alt="" class="image-post">
</p>