Plantillas PHP: cuestión de rendimiento
=======================================

_02-04-2009_

He leído dos posts bastante viejos (2006 y 2007) del gran Ricardo Galli sobre el [rendimiento y escalabilidad de Twitter](http://mnm.uib.es/gallir/posts/2007/04/13/1050/) y los [sistemas de plantillas PHP](http://mnm.uib.es/gallir/posts/2006/09/26/820/). En ambos posts las discusiones han sido muy ricas, y me llevan a dos grandes conclusiones: aunque el cache y escalabilidad horizontal son soluciones importantes para la alta disponibilidad de sitios web, es vital escribir buen código. Y en ese sentido, los sistemas de plantillas y los frameworks pueden jugar malas pasadas.

### Plantillas PHP, sin pseudo-lenguajes

En posts recientes he hablado de Smarty y de PHPTAL, dos sistemas de plantillas PHP que añaden un lenguaje nuevo a nuestras aplicaciones web, ya cargadas con PHP, SQL, XHTML, CSS, JavaScript y nosecuantas siglas más. Eso significa que, además de la curva de aprendizaje, hay que cargar el proyecto con un parser del pseudo-lenguaje. En el caso de Smarty, dicen ser rápidos, aunque hay un fork del proyecto llamado TemplateLight que dice ser más liviano que su "papá".

Existe un método para utilizar plantillas y no usar sistemas adicionales. Es una sistema sencillo, estúpidamente sencillo, pero eficiente y prácticamente con los mismos beneficios que usar Smarty u otros: plantillas PHP puro, embebiendo los códigos <!--?php y ?--> en los documentos HTML, pero separando las capas de lógica y vista para no caer en chapuzas.

Podemos crear una pequeña biblioteca de funciones para gestionar plantillas de este tipo y que los nombre de variables no causen conflictos. Utilizar plantillas PHP puro permite separar la presentación de la lógica, pero sin la bajada de rendimiento de los sistemas que parsean plantillas escritas en un pseudo-lenguaje como Smarty, del que ya se ha hablado por aquí. Reconozco que las plantillas PHP no tienen la legibilidad de Smarty o PHPTAL, pero se le acerca bastante.

### simpletpl.php (la librería del motor)

[php]&lt;?php

//Directorio donde están las plantillas
define(&quot;TPL_DIR&quot;, &quot;tpl&quot;);

//Array que alojará las variables de las plantillas
$tpl_vars = array();

//Asigna una variable a la plantilla
function tpl_asignar($nombre, $valor) {
global $tpl_vars;
$tpl_vars[$nombre] = $valor;
}

//Devuelve una variable de la plantilla
function tpl($variable) {
global $tpl_vars;
if (isset($tpl_vars[$variable])) return $tpl_vars[$variable];
else trigger_error(&quot;La variable '$variable' no existe&quot;, E_USER_ERROR);
}

//Carga la plantilla especificada
function tpl_cargar($plantilla) {
global $tpl_vars;
if (file_exists(TPL_DIR)) {
if (file_exists(TPL_DIR . &quot;/$plantilla&quot;)) {
include(TPL_DIR . &quot;/$plantilla&quot;);
} else trigger_error(&quot;La plantilla '$plantilla' no existe&quot;, E_USER_ERROR);
} else trigger_error(&quot;El directorio de plantillas especificado no existe. Verifique la constante TPL_DIR&quot;, E_USER_ERROR);
}

//Indenta un texto al $n - ésimo nivel
function tab($texto, $n) {
return preg_replace('/n/', &quot;n&quot; . str_repeat(&quot;t&quot;, $n), $texto);
}

?&gt;[/php]

### inicio.tpl.php (un ejemplo de plantilla cualquiera)

[php]&lt;html&gt;
&lt;head&gt;
&lt;title&gt;&lt;?php echo tpl('titulo') ?&gt;&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;&lt;?php echo tpl('titulo') ?&gt;&lt;/h1&gt;

&lt;?php foreach (tpl('posts') as $p) { ?&gt;
&lt;h2&gt;&lt;a href=&quot;&lt;?php echo $p['url'] ?&gt;&quot;&gt;&lt;?php echo $p['titulo'] ?&gt;&lt;/a&gt;&lt;/h2&gt;
&lt;div style=&quot;color: gray&quot;&gt;&lt;?php echo $p['creado'] ?&gt;&lt;/div&gt;

&lt;?php echo tab($p['cuerpo'], 2) ?&gt;

&lt;?php } ?&gt;

&lt;?php tpl_cargar(&quot;pie.tpl.php&quot;) ?&gt;&lt;/pre&gt;
&lt;h3&gt;pie.tpl.php (otra plantilla de ejemplo, para ser incluida)&lt;/h3&gt;
[php]&lt;div style=&quot;background: silver; padding: 10px&quot;&gt;2008 © Israel Viana&lt;/div&gt;
&lt;/body&gt;
&lt;/html&gt;[/php]

### index.php (script que obtiene los datos y carga la plantilla)

[php]&lt;?php
include(&quot;simpletpl.php&quot;);
$conexion = mysql_connect(&quot;localhost&quot;, &quot;root&quot;, &quot;root&quot;);
mysql_select_db(&quot;blog&quot;);

$q = mysql_query(&quot;SELECT * FROM posts ORDER BY id DESC LIMIT 5&quot;);

$posts = array();
while ($p = mysql_fetch_assoc($q)) {
$p['url'] = &quot;http://www.israelviana.es/post.php?id=&quot; . $p['id'];
$posts[] = $p;
}

tpl_asignar(&quot;titulo&quot;, &quot;Blog de Israel Viana&quot;);
tpl_asignar(&quot;posts&quot;, $posts);

tpl_cargar(&quot;inicio.tpl.php&quot;);

?&gt;[/php]

Repetir que este sistema es solo una prueba de concepto. Si quieres un sistema de plantillas PHP serio para tu proyecto, te recomiendo [PHPTemplate](https://drupal.org/phptemplate).