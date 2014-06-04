Aún más rendimiento con CssDispatcher
=====================================

_25-10-2009_

![](http://israelviana.es/wp-content/uploads/2009/10/cssdispatcher.png "cssdispatcher")En el artículo "Maneja las CSS como un profesional" veíamos algunas técnicas para insertar variables y funciones en las hojas de estilo, así como posibles mejoras del rendimiento en el cliente a la hora de servir las CSS, y para ello utilizábamos la biblioteca [CssDispatcher](http://cssdispatcher.sourceforge.net/).

Pues bien, en este post vamos a ver varias soluciones para aumentar el rendimiento en el servidor cuando servimos hojas de estilos, especialmente si son hojas de estilos tratadas con CssDispatcher.

Es importante diferenciar las mejoras de rendimiento [en el cliente](http://www.slideshare.net/vladungureanu/client-side-performace-in-web-applications) y el servidor. Mientras los benchmarks nos dicen que el 80% del tiempo de carga de una página lo causa el cliente (transferencias, renderizado, etc), para nosotros también es importante ese otro 20%, porque genera una **carga en el servidor** (acceso a disco duro, procesador, memoria...) que puede ponernos en aprietos si las visitas aumentan.

### Accesos al disco duro

De por sí, servir una hoja de estilos simple no suele ser muy costoso para un servidor web. Si un cliente solicita /main.css, el servidor abre el archivo, lo lee y lo devuelve. Además, si el sistema operativo implementa caché de ficheros, 1000 visitas no generarán 1000 accesos al disco duro, lo cual aumentará la velocidad y reducirá la carga del servidor.

Si queremos ir más allá y acelerar aún más los accesos a disco duro podemos poner los archivos del servidor un [sistema de archivos montado sobre la memoria RAM](http://es.wikipedia.org/wiki/Disco_RAM). Es decir, una porción del sistema de archivos que, en lugar de guardarse en el disco duro, lo hace sobre una porción de la memoria RAM. **No** es una técnica muy recomendable, porque aunque no es difícil montarlo, los datos desaparecen al apagar o reiniciar la máquina, por lo que habría que tener sincronizados los directorios en memoria con otros en el disco duro para que no se perdiesen.

### Procesamiento de la petición

Como ya he dicho antes y todo el mundo sabe, para el servidor web devolver un archivo estático no le cuesta más que abrirlo e irlo enviando por el socket. Pero si la hoja de estilos pasa por el compilador de PHP (por introducir códigos PHP en la hoja de estilos) entonces la cosa se complica un poco. Para acelerar el procesado de scripts es recomendable usar:

*   Una **caché de salida**, que evita tener que ejecutar repetitivamente el mismo código para cada petición.
*   Un **opcode cache**, que evita tener que compilar el código para cada petición (si hemos implementado caché de salida, para la mayoría de las peticiones no será necesario ni compilar ni ejecutar el código).
![Diagramas de flujo para el despacho de peticiones con caché](http://photos-g.ak.fbcdn.net/hphotos-ak-snc1/hs257.snc1/10433_1167616903116_1008733449_30410939_5875883_n.jpg)

Para la opcode cache existen herramientas como [APC](http://es.php.net/apc), [XCache](http://xcache.lighttpd.net/) o [eAccelerator](http://sourceforge.net/projects/eaccelerator/), que una vez instaladas y configuradas debidamente funcionan sin que tengamos que modificar el código de nuestra aplicación web. Puedes leer más sobre opcode cache en [PHP Accelerators](http://www.ducea.com/2006/10/30/php-accelerators/#more-134).

En cuanto a la caché de salida, hay principalmente dos caminos a seguir: gestionar la caché desde el servidor web o desde PHP. La ventaja del primero es que, al no ejecutar código PHP es un poco más rápido. Esto se puede lograr con el módulo Apache [mod_cache](http://www.vicente-navarro.com/blog/2007/12/02/usando-el-mod_cache-de-apache-para-que-el-mod_deflate-no-incremente-la-carga-del-servidor/). La otra posibilidad, gestionarlo con PHP, nos da más flexibilidad y control sobre la solución. En concreto, podremos guardar la caché en el disco duro o en memoria, con gestores de caché como [Memcached](http://en.wikipedia.org/wiki/Memcached) (mi preferido).

#### Caché con URL propia

Existe una tercera solución, y es utilizar una caché en disco duro sin procesar las peticiones por PHP. El funcionamiento es el siguiente: si tenemos una hoja de estilos generada con CssDispatcher en<samp>/estilos.css.php</samp>, podemos guardar la salida en un archivo y ponerlo accesible en<samp>/estilos.css</samp>. Al ser un archivo estático, se servirá tan rápido como una hoja de estilos normal. Este sistema tiene un problema, y es que si tenemos hojas de estilos específicas para navegadores, no resultará fácil servir una u otra de forma estática. La única solución aparente es utilizar redirecciones y estructuras condicionales en los ficheros de configuración de Apache.

### Algo de código, por favor

Vamos a implementar tres sistemas de caché: uno con Memcached, otro en disco y otro en disco accesible directamente.

#### Cache en Memcache

<pre lang="php">&lt;?php

include 'class.Css.php';

//Nombre del elemento en Memcache
$cache_name = 'moduleName_' . Css::getUserAgent();
//Tiempo de vida. Para archivos que no cambien mucho, poner valores altos
$cache_time = 3600*24;

//Conexión a Memcache
$mc = new Memcache();
$mc-&gt;connect('localhost');

//Si la salida no está en caché, se genera y se almacena
if (!$out = $mc-&gt;get($cache_name)) {

//Incluye la biblioteca CssDispatcher.
//No se incluye si no es necesaria
include 'class.CssDispatcher.php';

$estilos = new CssDispatcher;

//Crea una nueva hoja de estilos
$general = new Css('example.css.php');
//Asigna variables
$general-&gt;background = '#eee';
$general-&gt;border_color = 'red';
$general-&gt;header_size = 2.1;

//Crea otra hoja de estilos para navegadores WebKit
$another = new Css('example2.css.php', Css::UA_WEBKIT);
$another-&gt;bold = 'font-weight: bold';

//Añade las hojas de estilo al dispatcher
$estilos-&gt;add($general);
$estilos-&gt;add($another);

//Añade un aviso
$out = '/* Generated by CssDispatcher ' . strftime('%c') . " */n"
. $estilos-&gt;render(false, true, false, true);

//Guarda la salida en Memcache
$mc-&gt;set($cache_name, $out, null, $cache_time);
}

header("Content-Type: text/css");
echo $out;
die();

?&gt;</pre>
**Tiempo de ejecución (renovando la caché)**: 3.6480ms
**Tiempo de ejecución (utilizando la caché)**: 0.6439ms

#### Caché en disco duro

Este script tendrá será más lento y pesado para la CPU que el anterior, pero no necesita de servicios adicionales como Memcached, imposibles de instalar en alojamientos compartidos.
<pre lang="php">&lt;?php

include 'class.Css.php';

//Archivo de caché
$cache_name = 'cssCache/moduleName_' . Css::getUserAgent();
//Tiempo de vida, poner valores altos para archivos que no suelen cambiar
$cache_time = 30;

if (file_exists($cache_name)) {
if (date('U') - fileatime($cache_name) &gt; $cache_time) {
$regenerate = true;
} else {
$out = file_get_contents($cache_name);
}
} else {
$regenerate = true;
}

//Si la salida no está en caché, se regenera
if ($regenerate) {

//Carga la biblioteca CssDispatcher
include 'class.CssDispatcher.php';

$estilos = new CssDispatcher;

//Crea una nueva hoja de estilos
$general = new Css('example.css.php');
//Asigna variables
$general-&gt;background = '#eee';
$general-&gt;border_color = 'red';
$general-&gt;header_size = 2.1;

//Añade otra hoja de estilos para navegadores WebKit
$another = new Css('example2.css.php', Css::UA_WEBKIT);
$another-&gt;bold = 'font-weight: bold';

//Añade las plantillas al dispatcher
$estilos-&gt;add($general);
$estilos-&gt;add($another);

$out = '/* Generated by CssDispatcher ' . strftime('%c') . " */n"
. $estilos-&gt;render(false, true, false, true);

//Graba la salida en el fichero
file_put_contents($cache_name, $out);
}

header("Content-Type: text/css");
echo $out;
die();

?&gt;</pre>
**Tiempo de ejecución (renovando caché)**: 1.290ms
**Tiempo de ejecución (utilizando caché)**: 0.242ms

El último caso, una caché en ficheros accesibles públicamente, es más rápida y más ligera que la anterior, pero nos añade la tarea de actualizar la caché cuando nos interese, bien con una tarea programada (cron) o mediante algún otro mecanismo.
<pre lang="php">&lt;?php

include 'class.Css.php';
include 'class.CssDispatcher.php';

//Generamos hojas de estilos para todos los navegadores
$navegadores = array(Css::UA_IE6, Css::UA_IE, Css::UA_GECKO, Css::UA_WEBKIT);

//Para cada navegador generamos la hoja de estilos correspondiente
foreach ($navegadores as $navegador) {
//Archivo de caché
$cache_name = 'cssCache/estilos_' . $navegador . '.css';
//Tiempo de vida. Para archivos que no cambien mucho, poner valores altos
$cache_time = 30;

$estilos = new CssDispatcher;

//Crea una nueva hoja de estilos
$general = new Css('example.css.php');
//Asigna variables
$general-&gt;background = '#eee';
$general-&gt;border_color = 'red';
$general-&gt;header_size = 2.1;

//Añade las plantilla al dispatcher
$estilos-&gt;add($general);

//Hoja de estilos para WebKit
//No vale utilizar la detección de navegador de CssDispatcher
if ($navegador == Css::UA_WEBKIT) {
$another = new Css('example2.css.php');
$another-&gt;bold = 'font-weight: bold';
$estilos-&gt;add($another);
}

$out = '/* Generated by CssDispatcher ' . strftime('%c') . " */n"
. $estilos-&gt;render(false, true, false, true);

//Graba la salida en el fichero
file_put_contents($cache_name, $out);
}

echo "Caché de CSS actualizada";

?&gt;</pre>
&nbsp;

**Tiempo de ejecución del actualizador de caché**: 4.009ms
**Tiempo de obtención de la hoja de estilos**: 86ms (incluye la transmisión del fichero)

Ya que con este método no tenemos un sólo punto de entrada para las diferentes hojas de estilos, será necesario cargar una u otra CSS en función del navegador, a través de los artificios clásicos: [comentarios condicionales](http://es.wikipedia.org/wiki/Comentarios_condicionales) o [la cabecera User-Agent](http://php.about.com/od/learnphp/p/http_user_agent.htm). Pero tendremos que hacerlo manualmente, ya que al usar ficheros estáticos no se ejecuta CssDispatcher. Por ejemplo, según el ejemplo, si el navegador es Firefox tendremos que invocar<samp>cssCache/estilos_20.css</samp>.

Por mi parte, es todo por hoy. **¿Conoces alguna otra técnica para aumentar el rendimiento al servir CSS? ¿Has probado a cachear la salida de CssDispatcher?**