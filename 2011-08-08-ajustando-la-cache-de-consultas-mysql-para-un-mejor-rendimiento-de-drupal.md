Ajustando la cache de consultas de MySQL para un mejor rendimiento de Drupal
============================================================================

_08-08-2011_

¡Hola de nuevo! Después de un tiempo de inactividad voy a retomar el blog, aunque no prometo publicar muy asiduamente :-P

Continuando el hilo post anterior (mayo de 2010, ¡la de cosas que han pasado desde entonces!), vamos a ver cómo aumentar el rendimiento de un sitio Drupal, en este caso a través de la [cache de consultas de MySQL](http://dev.mysql.com/doc/refman/5.0/es/query-cache.html).

En /admin/reports/status/sql, Drupal muestra algunas estadísticas interesantes del servidor MySQL, que nos dan pistas sobre lo bien o mal optimizado que tenemos el sistema: número de consultas cacheadas, ordenaciones sin índices, etc.

En la explicación de alguno de los indicadores se indica "debe ser cero". Estos indicadores son Select_full_join, Select_range_check y Sort_scan. En términos generales, si estos valores son altos se debería modificar el esquema de la BD añadiendo índices, aunque si estamos hablando de Drupal esta situación no debería darse, ya que el esquema tiene índices correctamente establecidos.

Otro indicador interesante es Qcache_lowmem_prunes. Indica "el número de veces que MySQL tuvo que retirar consultas del caché porque se quedó sin memoria". En [mi caso](http://www.delejucartagena.es), este indicador mostraba el alarmante valor de 349102 (¡desde que se arrancó el servidor!).

[![](http://israelviana.es/wp-content/uploads/2011/08/drupal-informe-mysql1.png "drupal-informe-mysql")](http://israelviana.es/wp-content/uploads/2011/08/drupal-informe-mysql1.png)

Para arreglar esta situación vamos a aumentar el tamaño asignado a la caché de consultas. Lo haremos desde el fichero de configuración de MySQL (my.cnf):
<pre>query_cache_limit = 1M
query_cache_size = 32M</pre>
El primer parámetro es el tamaño máximo de las consultas "cacheables". Lo usaremos para evitar cachear consultas lentas o muy grandes, que suelen ser infrecuentes (y por tanto no tiene mucho sentido cachearlas).

El segundo parámetro es la cantidad de memoria que queremos asignar a la caché. Cuanta más memoria RAM tenga nuestro servidor, mayor valor podremos darle. En mi caso el servidor tiene 512MB, de los cuales alrededor de 200 están ocupados por el sistema y los servicios (Apache, el propio MySQL, memcache y los servicios de correo). Así que puedo aumentar el tamaño del cache hasta 50MB.

Después de reiniciar el servidor, lleva 95 días funcionando y arroja un valor de 3451 purgas. Muchas menos que con la anterior configuración.

Esta es una medida sencilla que ayuda a optimizar la cache de consultas de MySQL, un sistema que impacta en el rendimiento global del sistema, sobre todo si no tienes un sistema de caché fuera de MySQL.