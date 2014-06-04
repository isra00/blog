Wordpress multisite sin Wordpress multisite
===========================================

_01-01-2013_

Como [sabrás](http://www.usaelputogoogle.com/claudio.php?q=wordpress+multisite "Buscar información sobre Wordpress Multisite"), Wordpress permite alojar varios sitios diferentes con una misma instancia del CMS. Pero este sistema tiene algunas **limitaciones**:

*   Los sitios deben estar en subdominios o subdirectorios, por ejemplo blog1.example.org o example.org/blog1. No puedes usar nombres de dominio únicos (blog1.com, blog2.com). El plug-in [MU Domain Mapping](http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/) resuelve esta limitación, pero hace magia negra, y no mola.
*   Todos los sitios comparten los themes, plug-ins, usuarios y base de datos (problema de escalabilidad: no podrías usar varios servidores de MySQL). Eso no mola nada. En mi caso, porque tengo diferentes sitios con diferentes administradores y cada uno tiene sus themes y plug-ins.
Así que, viendo cómo funciona el proceso de carga de Wordpress, me he dado cuenta de que se puede hacer muy fácilmente un _hack_ que nos permita tener bases de datos, themes y plug-ins separados para cada sitio, y que se puedan mapear a dominios diferentes... más o menos como hace Drupal con el multi-site. Un multi-site de verdad.

Dicho así parece complicado, pero solo hay que pararse un segundo a pensar. Imaginemos que tenemos dos instancias completamente independientes, dos Wordpress normales y corrientes, cada uno con su base de datos, etc.

¿Qué tienen de diferente esas dos instancias?

La base de datos, el directorio wp-content y el fichero wp-config.php. Todo lo demás es exactamente igual. Duplicado.

Por tanto, si "engañamos" de alguna forma al cargador de Wordpress para cargue un wp-content y wp-config.php u otro en función del nombre de dominio, este se conectará a una u otra base de datos, ya que la información de conexión a MySQL está especificada en wp-config.php. Por tanto, habremos logrado Wordpress multi-site.

¿Y cómo hacemos ese truco para "engañar" a Wordpress y hacer que cargue uno u otro directorio wp-content y fichero de configuración wp-config.php? En primer lugar, organizaremos nuestros sitios. Por ejemplo, en la raíz de la instancia crearemos un directorio sites, dentro del cual pondremos los wp-config.php y wp-content de cada sitio, en sub-directorios nombrados con el nombre de dominio:

[![Estructura de ficheros para Wordpress multisite](http://israelviana.es/wp-content/uploads/2012/11/wp-interceptor-fs1.png "wp-interceptor-fs1")](http://israelviana.es/wp-content/uploads/2012/11/wp-interceptor-fs1.png)[![Carpetas para cada sitio](http://israelviana.es/wp-content/uploads/2012/11/wp-interceptor-fs2.png "wp-interceptor-fs2")](http://israelviana.es/wp-content/uploads/2012/11/wp-interceptor-fs2.png)[![Carpeta de un sitio en Wordpress multisite](http://israelviana.es/wp-content/uploads/2012/11/wp-interceptor-fs3.png "wp-interceptor-fs3")](http://israelviana.es/wp-content/uploads/2012/11/wp-interceptor-fs3.png)

Ahora viene lo bueno. Si echamos un vistazo a [wp-load.php](http://core.svn.wordpress.org/trunk/wp-load.php) vemos que aquí se carga wp-config.php y se declara la constante **WP_CONTENT_DIR**, que indica la ubicación del directorio wp-content.

[![Modificaciones en el loader (wp-load.php) para Wordpress multisite](http://israelviana.es/wp-content/uploads/2013/11/Captura-de-pantalla-de-2012-12-31-164354.png)](http://israelviana.es/wp-content/uploads/2013/11/Captura-de-pantalla-de-2012-12-31-164354.png)

Modificamos las declaraciones de WP_CONTENT_DIR y WP_CONFIG poniendo las rutas que nos interesen en función del nombre de dominio solicitado. El código final de wp-load.php quedaría así ([código completo de wp-load.php en GitHub](https://github.com/isra00/wordpress-multisite/blob/master/wp-load.php)):

[![wp-load.php interceptado (2)](http://israelviana.es/wp-content/uploads/2013/11/Captura-de-pantalla-de-2012-12-31-165233.png)](http://israelviana.es/wp-content/uploads/2013/11/Captura-de-pantalla-de-2012-12-31-165233.png)

![wp-load.php interceptado (1)](http://israelviana.es/wp-content/uploads/2013/11/Captura-de-pantalla-de-2012-12-31-165251.png)

Para tener todo listo solo faltan dos detalles: la configuración de Apache y la creación de la base de datos.

### Crear la base de datos para el Wordpress multisite

Para crear la base de datos de cada site, simplemente tendremos que clonar la BD de un Wordpress recién instalado para los demás sitios. No sería difícil automatizarlo con un script.

### Configurar Apache

La configuración necesaria para un Wordpress multi-sitio con esta receta es muy sencilla: crear tantos _vhosts_ como queramos, haciendo que todos ellos tengan el mismo DocumentRoot. Por ejemplo:

[sourcecode]

ServerName wp1.com
ServerAlias  wp2.com wp3.com
DocumentRoot /var/www/wp-multisite

[/sourcecode]

Otro detalle importante: para que los administradores de los diferentes sitios puedan acceder a sus directorios wp-content particulares (y solo a los suyos), puedes crear usuarios del sistema (Linux, por supuesto) cuyo directorio $HOME es el subdirectorio de sites que le corresponde. Por ejemplo, para el usuario administrador de blog1.com, su directorio $HOME sería /var/www/wp-multisite/sites/blog1.com, suponiendo que tengamos la instancia de Wordpress en /var/www/wp-multisite.

### Conclusión

Al final, con este sistema lo que tenemos es prácticamente igual a tener instancias independientes de Wordpress, pero con algunas ventajas interesantes:

*   Actualizaciones del núcleo centralizadas, ya que tenemos una sola instancia. Ya no tendrás que ir actualizando sitio por sitio. Eso sí, los plug-ins y themes se deben actualizar independientemente.
*   Ahorro de memoria: ahorras disco duro, y sobre todo ahorras RAM si usas APC (si no lo estás usando... ¡deberías hacerlo!). Una instancia de Wordpress ocupa más o menos 20MB en APC; imagínate si tenemos 100 instancias de Wordpress en el servidor... ¡compartiendo el código podemos ahorrar Gigas!

### Desventajas

*   Es un sistema experimental que aún no he probado en producción, así que puede fallar por cualquier lado :-P
*   No existe una forma automatizada y amigable de crear nuevos sitios. Debes crearlos a mano a partir de plantillas de wp-config.php, wp-content y base de datos.
*   Si se actualiza el núcleo Wordpress con el sistema automático, las actualizaciones que afecten a la base de datos solo afectarán a la del sitio desde el cual se haya actualizado el núcleo.
*   Seguridad: a no ser que se establezcan mecanismos adicionales, un administrador de un sitio puede romper con facilidad las otras instancias.