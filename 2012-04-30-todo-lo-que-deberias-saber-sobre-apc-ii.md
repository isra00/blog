Todo lo que deberías saber sobre APC (II)
=========================================

_30-04-2012_

[![Configurar APC: directivas de configuración](http://israelviana.es/wp-content/uploads/2012/04/directivas-apc.png "Configurar APC: directivas de configuración")](http://israelviana.es/wp-content/uploads/2012/04/directivas-apc.png) **Aviso**: si no has leído antes el [primer artículo de la serie](http://israelviana.es/todo-lo-que-deberias-saber-sobre-apc-i/) deberías hacerlo.

### CONFIGURANDO APC para una óptima optimización optimizada

Como ya sabrás, las directivas de configuración se pueden cambiar en los ficheros .ini de configuración de PHP y sus extensiones. En el caso de Linux, probablemente el fichero esté en `/etc/php5/apache2/conf.d/apc.ini`, aunque también puedes añadir directivas en php.ini o cualquier otro fichero .ini del directorio conf.d/, ya que todos son cargados por el motor de PHP.

De [todas las directivas de configuración que APC ofrece](http://www.php.net/manual/en/apc.configuration.php), creo que estas son las más jugosas:

*   `apc.stat`: indica si APC debe comprobar, a cada petición, si el archivo solicitado ha cambiado. En un servidor de producción es interesante desactivar esta opción ya que nos ahorraremos esa llamada al sistema. Pero ¡cuidado con los cambios de código! Para recompilar un script debes limpiar manualmente el APC o bien reiniciar Apache (con FastCGI no serviría).
*   `apc.stat_ctime`: comprueba que un archivo ha cambiado a través de los inodes, en lugar de con mtime.
*   `apc.shm_size`: especifica la memoria reservada para APC, en MB. Por defecto es 30.
*   `apc.max_file_size`: tamaño máximo de script cacheable.
*   `apc.lazy_functions` y `apc.lazy_classes`: cargan las funciones y clases de los includes sólo cuando son necesarios.
*   `num_files_hint`: aproximadamente cuántos scripts tienes en tu servidor. Ayuda a APC a optimizar sus índices internos. Si no estás seguro, mejor dejarlo a 0.
*   `user_entries_hint`: aproximadamente cuántas entradas de usuario guardarás. No estás seguro, mejor déjalo en 0.
*   `apc.cache_by_default` y `apc.?lters`: si pones `apc.cache_by_default = 1`, APC sólo cacheará los scripts cuyo nombre coincida con la expresión regular que especifiques en `apc.filters`. Interesante si tienes algunos scripts que sabes que van a cambiar muy frecuentemente (por ejemplo, durante un período de actualización) y has sido tan machote de desactivar `apc.stat. `
*   `apc.include_once_override`: se trata de una característica experimental, que se supone que optimiza las llamadas `include_once` y `require_once` para que consuman menos recursos. Dentro de algún tiempo tendremos más información sobre esta funcionalidad.

### La extraña pero maravillosa directiva apc.rfc1867

Entre todas estas directivas de configuración destaca rfc1867, que obviamente hace referencia al documento [RFC 1867](http://www.faqs.org/rfcs/rfc1867.html#b), el cual especifica la subida de ficheros por HTML/HTTP. Pero ¿esto a qué viene?

**Pues viene a que con APC, además de cachear los opcodes y guardar valores en la cache, también puedes controlar el progreso de las subidas de ficheros.**

Sí, amigos. Si activamos la directiva `apc.rfc1867`, por cada fichero que se suba a tu servidor APC creará e irá actualizando una _user entry_ con información sobre la subida. Esta entrada contendrá algo así como:
<div>
<pre>Array
(
[total] =&gt; 1142543
[current] =&gt; 1142543
[rate] =&gt; 1828068.8
[filename] =&gt; test
[name] =&gt; file
[temp_filename] =&gt; /tmp/php8F
[cancel_upload] =&gt; 0
[done] =&gt; 1
)</pre>
Es decir, el tamaño total del fichero, los bytes transferidos (current), la velocidad de subida (rate) y otros parámetros. Con esta valiosa información puedes hacer una bonita barra de progreso para indicar a tus usuarios cómo van sus subidas. Si quieres más detalles sobre cómo utilizar esta característica, te remito a [este artículo](http://www.ultramegatech.com/2008/12/creating-upload-progress-bar-php/) y, como siempre, al [manual](http://www.php.net/manual/en/apc.configuration.php#ini.apc.rfc1867) oficial.

### Si te has quedado con ganas de más

*   [apc@facebook](http://es.scribd.com/doc/88689/ApcFacebook): presentación de 2007 donde se explica el funcionamiento de APC y se exponen los valores de configuración en los servidores de Facebook. Además explican su interesantísimo sistema para distribuir configuraciones en múltiples servidores a través de APC.
*   [2bits cuentan un caso interesante](http://2bits.com/articles/high-php-execution-times-drupal-and-tuning-apc-includeonce-performance.html) con Drupal y la discusión es también muy rica.
*   ¡[El manual](http://www.php.net/manual/en/book.apc.php)! No me canso de decirlo.

### Esto no termina aquí

Te toca a ti. ¿Tienes experiencia con APC? ¿Has notado la mejora de rendimiento en tus servidores de producción? ¿Cómo lo has configurado? ¿Me das la contraseña de root?

</div>