Patrones de desarrollo web: componentes HTML reutilizables
==========================================================

_02-06-2009_

Cuando construimos aplicaciones con muchos formularios u otras secciones con fragmentos repetitivos, a veces utilizamos componentes, como puede ser un cuadro desplegable (combo box u option select, llámale como quieras) para elegir una ciudad. Ya que las páginas se generan en el servidor (PHP, Java, Ruby...), se han inventado diversos artificios para reutilizar estos componentes: simples funciones o variables que devuelven el código HTML deseado, mini-plantillas (Smarty), elements de CakePHP, etc.

Pero ¿y si no estamos generando esos formularios en el servidor, sino dinámicamente en JavaScript? La solución en el cliente es **disponer de componentes de diseño como fragmentos de HTML invocables por AJAX**. Así de sencillo.

Por ejemplo, imaginemos que nuestra aplicación/web tiene muchos formularios en los que elegir una ciudad en un control option select. Si estamos generando formularios a través de JavaScript, sería muy útil disponer de una URL invocable a través de AJAX que nos devolviese algo como

<pre lang="html">&lt;select&gt;
&lt;option value="1"&gt;Vigo&lt;/option&gt;
&lt;option value="2"&gt;Murcia&lt;/option&gt;
&lt;option value="3"&gt;Sevilla&lt;/option&gt;
&lt;/select&gt;</pre>

Y si agregamos algunas opciones...

<pre lang="php">&lt;?php $opciones = array("Vigo", "Murcia", "Sevilla") ?&gt;
&lt;select name="&lt;?php echo $_GET['nombre'] ?&gt;"&gt;
&lt;?php foreach ($opciones as $n=&gt;$nombre) { ?&gt;
&lt;option value="&lt;?php echo $n ?&gt;" &lt;?php if ($_GET['valor'] == $n) echo "selected=""" ?&gt;&gt;&lt;?php echo $nombre ?&gt;&lt;/option&gt;
&lt;?php } ?&gt;
&lt;/select&gt;</pre>

Entonces tenemos algo como <samp>form_ciudades.php?nombre=xxx&amp;valor=yyy</samp> que nos devolverá un option select de nombre xxx con el valor yyy seleccionado por defecto.

Si combinamos esto con alguna función que envuelva peticiones AJAX y nos devuelva el contenido requerido de forma sencilla, conseguiremos un código fácilmente legible. Aquí va un ejemplo real, que estoy utilizando para una práctica de la universidad:

<pre lang="javascript">$("#detalles &gt; table").html(
"&lt;tr&gt;&lt;td&gt;Aerolínea:&lt;/td&gt;&lt;td&gt;"
+ ajax("sn_aerolineas.php?nombre=aerolinea&amp;valor=1")
+ "&lt;/td&gt;&lt;/tr&gt;"
+ "&lt;tr&gt;&lt;td&gt;Origen:&lt;/td&gt;&lt;td&gt;"
+ ajax("sn_localidades.php?nombre=origen&amp;valor=1")
+ "&lt;/td&gt;&lt;/tr&gt;"
+ "&lt;tr&gt;&lt;td&gt;Destino:&lt;/td&gt;&lt;td&gt;"
+ ajax("sn_localidades.php?nombre=destino&amp;valor=1")
+ "&lt;/td&gt;&lt;/tr&gt;");</pre>

Para quien no conozca jQuery, la función $("#detalles > table") devuelve la tabla que hay dentro del elemento del contenedor "detalles", y la función html() cambiará su innerHTML. <samp>ajax</samp> es una pequeña y sucia función que me devuelve directamente una respuesta AJAX.

Este patrón se puede aplicar para muchos más casos, como selectores de fecha (input + calendario), o incluso llamadas que devuelvan datos mucho más pequeños, como la foto de un usuario o una lista de categorías. Pero recuerda que este patrón debe ser aplicado sólamente cuando se generan elementos dinámicamente. Es decir, **usa AJAX sólo cuando sea necesario para mejorar la usabilidad y/o el rendimiento de tu sitio**.