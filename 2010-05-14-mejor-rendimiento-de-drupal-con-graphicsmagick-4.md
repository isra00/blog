Mejor rendimiento de Drupal con GraphicsMagick
==============================================

_14-05-2010_

Un pequeño (y espero útil) apunte antes de que penséis que he abandonado completamente el blog :-(

Se trata de un consejo para mejorar el rendimiento de nuestro sitio Drupal. El sistema utiliza por defecto la biblioteca GD para manipular imágenes (por ejemplo, generar miniaturas). El módulo [ImageAPI](http://drupal.org/project/imageapi/) inserta una capa de abstracción en Drupal que permite elegir qué biblioteca gráfica usar, GD o ImageMagick. Es mejor utilizar ImageMagick, ya que es una biblioteca independiente de PHP y, por ello obtendremos mejor rendimiento total en Drupal, y además es más estable (si falla GD puede caer todo el motor de PHP, sin embargo, si falla ImageMagick no caerá, ya que es un binario diferente).

Pero aún mejor: existe un proyecto llamado [GraphicsMagick](http://www.graphicsmagick.org/), que es un fork de ImageMagick y que es más estable y, sobre todo, con mejor rendimiento, ya que consume menos memoria y es más rápida. Y lo que es mejor, tiene la misma API que ImageMagick (el comando

<samp>convert</samp>), con lo cual podemos _engañar_ a ImageAPI diciéndole que use ImageMagick cuando en realidad tenemos instalado GraphicsMagick.

Para lograr esto no tienes más que instalar GraphicsMagick y el [módulo ImageAPI](http://drupal.org/project/imageapi/) de Drupal, en dos sencillos pasos. Supongamos que trabajamos con un servidor Debian/Ubuntu (en otros sistemas operativos no debería ser muy diferente):

1.  Instalamos GraphicsMagick:
<kbd>apt-get install graphicsmagick-imagemagick-compat</kbd>Podemos probar que GraphicsMagick funciona escribiendo convert en la consola:
<kbd>isra@salon:~$ convert
GraphicsMagick 1.3.5 2009-01-26 Q8 http://www.GraphicsMagick.org/
Copyright (C) 2002-2009 GraphicsMagick Group.
Additional copyrights and licenses apply to this software.
See http://www.GraphicsMagick.org/www/Copyright.html for details.</kbd>

Usage: convert [options ...] file [ [options ...] file ...] [options ...] file

[más opciones]

*   Instalamos el módulo ImageAPI y lo activamos en /admin/build/modules (módulos ImageAPI e ImageAPI ImageMagick).
*   Accedemos a la configuración de ImageAPI en Administración &gt; Configuración del sitio &gt; ImageAPI (/admin/settings/imageapi) y veremos el siguiente mensaje: "<cite>The ImageAPI ImageMagick module is the only enabled image toolkit. Drupal will use it for resizing, cropping and other image manipulations</cite>".
*   Si también hemos activado el módulo ImageAPI GD2, nos dará a elegir (seleccionamos ImageAPI ImageMagick como opción por defecto):

![](http://israelviana.es/wp-content/uploads/2010/05/imageapi.jpg "ImageAPI en Drupal")
¡Y listo! Fácil y rápido. Podemos comprobar que funciona accediendo a Administración &gt; Configuración del sitio &gt; ImageAPI &gt; Configurar (/admin/settings/imageapi/config), lo que nos mostrará información sobre GraphicsMagick (versión, tipos soportados, etc):

[![](http://israelviana.es/wp-content/uploads/2010/05/imageapi-graphicsmagick.jpg "ImageAPI en Drupal con GraphicsMagick")](http://israelviana.es/wp-content/uploads/2010/05/imageapi-graphicsmagick.jpg)

### ¿He hecho benchmarks para comprobar la ganancia de rendimiento con respecto a ImageMagick?

Pues no, lo siento. Pero los chicos de GraphicsMagick [sí los han hecho](http://www.graphicsmagick.org/benchmarks.html), y son bastante jugosos.

### Pero... ¿no es necesaria la extension imagick de PHP?

No, no es necesaria, ya que ImageAPI interactúa con ImageMagick/GraphicsMagick a través del binario
<samp>convert</samp>

Iré escribiendo algunos artículos más sobre técnicas fáciles para optimizar el rendimiento de Drupal.