Maneja las CSS como un profesional
==================================

_14-09-2009_

Una parte importante de cualquier aplicación o sitio web son los estilos CSS. Las hojas de estilos se suelen escribir aisladas de la aplicación, sus tecnologías y metodologías. Además, suelen ser servidos como archivos estáticos, de forma que no pasan por la base de datos ni el resto de la aplicación. Existen buenas prácticas relacionadas con la legibilidad y mantenibilidad de las hojas de estilos, pero pocas veces se opta por mezclar las CSS con las aplicaciones, entre otras cosas porque servirlos como ficheros estáticos es más rápido que procesarlos con PHP, y porque a menudo los diseñadores que escriben los estilos no conocen las tecnologías de programación.

No obstante, como se explica en [Supercharge Your CSS with PHP Under the Hood](http://net.tutsplus.com/tutorials/php/supercharge-your-css-with-php-under-the-hood/) y demuestran algunas aplicaciones como Plone o phpMyAdmin, mezclar las tecnologías de programación con el lenguaje de estilos CSS puede aportarnos más potencia y comodidad a la hora de escribir, mantener y aumentar el rendimiento de las _stylesheets_. Y por el mismo precio también te presentaré [CssDispatcher](http://cssdispatcher.sourceforge.net), una biblioteca que te ayudará a hacer todo eso casi sin esfuerzo.

### Variables y funciones en CSS

En programación, si vas a utilizar varias veces un dato, lo almacenas en una variable para manejarlo más cómodamente. El mismo principio podemos aplicar, por ejemplo, con los colores en una hoja de estilos. De este modo evitamos repetir (es decir, recordar o copiar-pegar) códigos hexadecimales. Por ejemplo, el siguiente estilo:
<pre class="css">.paginacion { border: 1px solid red; }
.paginacion a { color: red; }</pre>
...quedaría de este modo:
<pre class="css">.paginacion { border: 1px solid &lt;?=$rojo ?&gt;; }
.paginacion a { color: &lt;?=$rojo ?&gt;; }</pre>
Como ves, he utilizado las etiquetas cortas y el operador de impresión `&lt;?= `en lugar de `echo `para abreviar. Establecer variables para colores facilita la tarea de crear esquemas de colores intercambiables (y más legibles):
<pre class="css">body { color: &lt;?=$color_texto ?&gt;; }
a { color: &lt;?=$color_enlace ?&gt;; }
h1 { color: &lt;?=$color_primario ?&gt;; }

#divCentral { border: 1px solid &lt;?=$color_primario ?&gt;; }</pre>
Como ya he comentado, phpMyAdmin hace uso de esta técnica. Aquí tenemos un pequeño ejemplo:
<pre class="css">th {
font-weight:        bold;
color:              &lt;? echo $GLOBALS['cfg']['ThColor']; ?&gt;;
background:         &lt;? echo $GLOBALS['cfg']['ThBackground']; ?&gt;;
}</pre>
En este caso no se usa el operador `=` para imprimir, y sí `echo`. Es recomendable hacer lo mismo ya que en muchos servidores (sobre todo con las versiones más recientes de PHP) las etiquetas cortas están desactivadas.

Además de colores, puedes utilizar las variables para calcular medidas relativas. Por ejemplo, si tenemos un layout de 800px de ancho con dos columnas, una de 600px y otra de 200, podemos expresarlo con medidas relativas (%) para hacer más accesible nuestras hojas de estilo:
<pre class="css">#columna1 { width: &lt;?php echo ($ancho_columna1/800)*100 ?&gt;%; }
#columna2 { width: &lt;?php echo ($ancho_columna2/800)*100 ?&gt;%; }</pre>
También podemos utilizar propiedades completas como variables:
<pre class="css">h1 { &lt;?php echo $negrita ?&gt;; }</pre>
Donde `$negrita` valdría `font-weight: bold`.

Llegados a este punto, seguramente estés pensando "muy bonito, pero **¿dónde defino las variables?**". En el tutorial de NetTuts los ejemplos muestran las declaraciones de variables en el mismo fichero que las CSS, pero no creo que sea una buena práctica. phpMyAdmin tiene ficheros separados donde se definen los valores, aunque utilizar el array $_GLOBALS no es muy elegante, sobre todo si tu aplicación es orientada a objetos.

Como ya adelanté al comienzo, vamos a ver una biblioteca llamada CssDispatcher que facilita la gestión de CSS con PHP. Esta librería propone un método concreto de definir las variables de los estilos, de forma que se identifiquen fácilmente y además los nombres no causen conflictos con el resto de variables de la aplicación. Este método es sencillamente añadir las variables a una instancia de la clase Css a través del tipado dinámico:
<pre lang="php">$general = new Css('general.css.php');
$general-&gt;fondo = '#eee';
$general-&gt;color_borde = 'red';
$general-&gt;cabecera = 2.1;</pre>
Aunque las variables son introducidas en el objeto, al usarlas en la plantilla CSS sólamente tenemos que especificar su nombre, como variables "tradicionales":
<pre class="css">body { background: &lt;?php echo $fondo ?&gt;; }</pre>
De este modo podemos asignar variables a cuantas hojas de estilos queramos, sin que interfieran los nombres:
<pre lang="php">$general = new Css('general.css.php');
$general-&gt;altura_cabecera = '5em';

$ie_hacks = new Css('ie_hacks.css.php');
$ie_hacks-&gt;altura_cabecera = '5.1em';</pre>
Ah, por cierto, las variables también pueden ser de tipo función anónima. Por ejemplo...
<pre lang="php">$general-&gt;layout = function($proporcion) {
return $proporcion / 800;
}</pre>
...que se podría aplicar de este modo:
<pre lang="php">.columna1 { width: &lt;?=$layout(100) ?&gt;em; }</pre>
Un último apunte sobre CSS "plantilladas": te recomiendo que las nombres con extensión `.css.php`, como se ve en los ejemplos, o bien `.css.tpl`. Utilizar la extensión simple `.css` puede llevar a confusiones... pero es sólo un consejo.

### Aumentando el rendimiento con CssDispatcher

Tanto si usamos variables en las CSS como si no, podemos mejorar el rendimiento si las procesamos con PHP, tanto en el servidor (tardamos menos en procesarlas) como en el cliente (le llegan antes).

Un ejemplo es el envío de varias hojas de estilo como un solo fichero. Mientras las buenas prácticas de CSS recomiendan separar las hojas de estilo por secciones o por navegadores (por ejemplo, ie-hacks.css, webkit.css, etc), las buenas prácticas de rendimiento nos aconsejan minimizar el número de peticiones HTTP. Así pues, ¿qué escogemos: mantenibilidad o rendimiento? Pues yo me quedo con las dos: escribamos las CSS en ficheros separados y juntémoslas a la hora de servirlas.

CssDispatcher nos permite hacerlo de una forma muy sencilla. Una vez que hemos creado instancias de la clase Css para cada hoja de estilo, las añadimos a un objeto CssDispatcher:
<pre lang="php">$general = new Css('ejemplo.css.php');
$general-&gt;fondo = '#eee';

$otra = new Css('example2.css.php');
$otra-&gt;negrita = 'font-weight: bold';

//Creamos un dispatcher
$estilos = new CssDispatcher;

//Añadimos las CSS al dispatcher
$estilos-&gt;add($general);
$estilos-&gt;add($otra);

$estilos-&gt;render();</pre>

### Cross-browser

Otro de los problemas comunes cuando se escriben estilos es la compatibilidad entre navegadores. No todos renderizan las CSS de igual forma, así que es habitual escribir hojas de estilo con propiedades específicas para Internet Explorer, Safari/Chrome, etc. Dependiendo del que esté utilizando el usuario se envía una u otra.

Se han venido utilizado tres métodos diferentes para cargar hojas de estilos específicas:

*   **Comentarios condicionales** (sólo funcionan en IE): para añadir CSS específicas de Internet Explorer basta con envolver la línea de llamada (
`&lt;link rel="stylesheet"...`) con las directivas`&lt;!--[if IE 6]&gt;`y`&lt;![endif]--&gt;`.
*   **JavaScript**: una vez cargada la página, se ejecuta un script que comprueba el navegador y carga dinámicamente la hoja de estilos correspondiente. En consecuencia, los usuarios que no ejecuten JavaScript (por seguridad o por usar un navegador viejo/limitado) no podrán cargar hojas de estilo específicas.
*   **En el servidor:**: antes de enviar la página, el script que la genera comprueba la cabecera HTTP User-Agent y escribe las invocaciones correspondientes al generar HTML.
Elegiremos el último método por ser más seguro y compatible. Con CssDispatcher definir CSS específicas de navegador es tan fácil como añadir un segundo parámetro en el constructor de la clase `Css`:
<pre lang="php">//Enviará la hoja de estilos si el navegador del usuario es Internet Explorer 6
$ie_hacks = new Css('ie_hacks.css.php', Css::UA_IE6);</pre>
Puedes especificar las siguientes familias de navegadores: Internet Explorer 6 (`Css::UA_IE6`), Internet Explorer &gt;6 (`Css::UA_IE`), Gecko (`Css::UA_GECKO`), WebKit (`Css::UA_WEBKIT`). También puedes utilizar los sinónimos `UA_MOZILLA`, `UA_FIREFOX`, `UA_SAFARI`y `UA_CHROME `respectivamente. Para más información puedes ver la <a>referencia de CssDispatcher</a>.

### En resumen

Hemos visto varias técnicas que nos ayudan a manejar de forma más eficaz y eficiente las hojas de estilos. Puedes utilizar las que necesites, tanto si quieres hacerlo de forma manual como utilizando CssDispatcher (u otras bibliotecas, si las encuentras dímelo!). A saber:

*   Asignación de variables en espacios de nombres seguros.
*   Hojas de estilos específicas de un navegador.
*   Minimización del código CSS.
*   Unión de hojas de estilos para un envío único.

### Rendimiento en el servidor con cache

No hay que olvidar que generar estilos con CssDispatcher provoca una carga en el servidor por los accesos a disco (para obtener las CSS y otro para obtener los scripts) y por el tiempo de compilación y ejecución. Por ello, una buena técnica para aumentar la velocidad y reducir sobremanera el _throughput_ es cachear el resultado final de la ejecución de CssDispatcher. Para cachear la salida de un script existen varias técnicas:

*   **Cache en el servidor web**: la salida de una petición se almacena en memoria o disco y se sirve sin ejecutar nada de PHP. En Apache esto es posible con el módulo mod_cache y sus motores de almacenamiento, [mod_disk_cache](http://httpd.apache.org/docs/2.0/mod/mod_disk_cache.html) y [mod_mem_cache](http://httpd.apache.org/docs/2.0/mod/mod_mem_cache.html).
*   **Cache a través de PHP**: esta técnica hace básicamente lo mismo que la anterior, pero en vez de estar gestionada por el servidor web se gestiona con scripts PHP. Por ello, aunque no es necesario compilar y ejecutar los scripts de CssDispatcher sí es necesario ejecutar el script de control de cache, por lo que esta solución no es tan buena como mod_cache. No obstante, en servidores compartidos y otros entornos de escaso control es una buena solución. Además, el script de control de cache se puede cachear con los sistemas de cache de bytecodes como [APC](http://www.php.net/apc) o [eAccelerator](http://sourceforge.net/projects/eaccelerator/), aunque eso ya es un tema aparte.

##### Artículo relacionado

*   [Aún más rendimiento con CssDispatcher](http://israelviana.es/aun-mas-rendimiento-con-cssdispatcher-4/)