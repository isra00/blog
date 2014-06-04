Migrando el blog a Wordpress
============================

_29-11-2011_

Hace algunos días el blog dejó de funcionar, probablemente por una actualización de los servidores de mi proveedor, 1&amp;1, que invalidaba las reglas de Apache rewrite que escribí. Sí, son cosas que apestan de los servidores compartidos, pero lo he aprovechado para "matar moscas a cañonazos" y migrar todo el blog a Wordpress.

El antiguo blog funcionaba con un software PHP escrito por mí siguiendo el [patrón Multivista](http://israelviana.es/tag/multivista/) y usaba [plantillas Smarty](http://israelviana.es/5-razones-para-no-usar-smarty-o-similares-4/ "Usé Smarty pero luego me di cuenta de que no mola"). Mis razones para migrar a Wordpress:

1.  Compatibilidad con los servidores: el `.htaccess` de Wordpress es más tolerante que el mío a los cambios caprichosos de 1&amp;1.
2.  Tener un software más seguro, más probado y más potente.
3.  Los experimentos que haga con el blog (sobre todo de cosas relacionadas con web semántica) los escribiré como plug-ins de Wordpress y así otras personas los podrán probar.
4.  El back-end de Wordpress es infinitamente mejor que el mío.
5.  Utilizar la plantilla Twenty Eleven como base para el diseño, por su calidad de código HTML5 y CSS.
6.  Seguir aprendiendo Wordpress.
7.  Rendimiento: WP-Cache y WP Super Cache me ahorrarán un trabajo que me daba pereza empezar.
Este ha sido, a grandes rasgos, el procedimiento que he seguido para la migración:

### Uno: migrando los artículos

Esto no ha sido difícil, ya que he escrito un sencillo plug-in que se conecta a la BD antigua, recorre todos los artículos (con sus tags) y los introduce en Wordpress con la función [`wp_insert_post()`](http://codex.wordpress.org/Function_Reference/wp_insert_post).

### Dos: migrar los comentarios

Cuando ya tenía todo listo me di cuenta de que había olvidado migrar los comentarios... así que los migré no programando un plug-in (como hice con los artículos) sino exportando los comentarios de la antigua base de datos a CSV, adaptando las columnas a la tabla `wp_comments` e importándolo a esta con phpMyAdmin.

### Tres: adaptando el theme

Me he basado en Twenty Eleven, el theme por defecto de Wordpress 3.2.1, que utiliza HTML5, soporta widgets, traducción y muchas otras cosas. La labor de _theming_, cuando ya tienes el diseño maquetado (el antiguo blog) es más laborioso que difícil, así que en un par de horas estuve despachado. También he aprovechado para hacer algunos cambios en el diseño, intentando que quede más limpio y simple, y cambiando los enlaces para compartir los posts en redes sociales.

### Cuatro: organizando el contenido

Unos minutos de taxonomías, tipos de entradas, categorías, etc para tenerlo todo bien ordenadito.

### Cinco: desplegando

Con el contenido migrado y organizado y el theme adaptado, ya puedo instalar en el servidor una nueva instancia de Wordpress, subir el theme e importar el contenido con la herramienta [Importar/Exportar de Wordpress](http://codex.wordpress.org/Function_Reference/wp_insert_post). El resto de tareas las hago directamente en el servidor de producción.

### Seis: instalar plug-ins y retoques finales

Con el blog ya montado y funcionando sobre Wordpress, completo la sección de contacto con [Contact Form 7](http://wordpress.org/extend/plugins/contact-form-7/), enlazo el RSS con [Feedburner](http://wordpress.org/extend/plugins/primary-feedburner/), inserto el código de Google Analytics en el theme, añado el plug-in para resaltado de código fuente [WP-Syntax](http://wordpress.org/extend/plugins/wp-syntax/ "plug-in para resaltado de sintaxis") y reviso que no hay enlaces rotos en los artículos con [Broken Link Checker](http://wordpress.org/extend/plugins/broken-link-checker/ "plug-in para Wordpress que encuentra enlaces rotos en el contenido del sitio").

### Siete: redirecciones 301 para no perder posicionamiento en buscadores

Con el blog en Wordpress, las antiguas URL de los artículos se pierden, así que para evitar que los buscadores reindexen todo el contenido de nuevo y pierda el posicionamiento de mis artículos, he configurado redirecciones para cada uno de los artículos. La cabecera 301 "moved permanently" le indica a los buscadores que la página ha sido movida permanentemente a otra dirección, pero que sigue siendo la misma página.

Las redirecciones las he puesto en un script que es llamado desde la plantilla 404.php del theme, de forma que cuando se solicita una URL antigua se produce un error 404 controlado por Wordpress, pero antes de que se lance la cabecerar 404 y página de error, mi script comprueba si esa URL es de las antiguas y redirige, con una cabecera 301, a la URL correcta.

**ACTUALIZACIÓN 16/dic/2012**: después de un año con WP-Syntax he cambiado a SyntaxHighlighter Evolved, un plug-in mucho mejor para resaltado de sintaxis. La diferencia básica es que este muestra los números de línea. Además, WP-Syntax fallaba al escapar los caracteres &lt; y &gt;.