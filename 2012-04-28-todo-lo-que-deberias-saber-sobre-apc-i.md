Todo lo que deberías saber sobre APC (I)
========================================

_28-04-2012_

Una de las formas más fáciles, a la vez que efectivas (un 300% de media), de hacer que tu sitio web PHP sea más rápido es usar un _opcode cache_. Hay varios en el mercado: APC, eAccelerator, XCache... vamos a ver APC por ser libre, el más fácil de manejar, extendido y el que mejores resultados arroja en los [benchmarks](http://2bits.com/articles/benchmarking-drupal-with-php-op-code-caches-apc-eaccelerator-and-xcache-compared.html "Una comparativa de varios opcode cache ejecutando Drupal").

### Pero... ¿qué es un opcode cache?

[![Diagrama de flujo de APC](http://israelviana.es/wp-content/uploads/2012/04/apc_flowchart.gif "Diagrama de flujo de APC")](http://israelviana.es/wp-content/uploads/2012/04/apc_flowchart.gif)Como seguramente sabrás, PHP es un lenguaje interpretado. Esto significa que cada vez que se solicita la ejecución de un script (o sea, cuando el usuario navega a una página concreta), este se compila y se ejecuta de forma automática, dando la sensación de que "no hay que compilar PHP". Es un mito bastante extendido que PHP no se compila.** Falso. PHP sí se compila, pero de forma automática**. Just In Time (JIT), que dicen los anglosajones.

Este sistema de compilación JIT tiene la ventaja de una gestión más simple, sobre todo cuando tienes un script ya funcionando en el servidor y quieres modificarlo. Con PHP simplemente tienes que sustituir el fichero... y listo, la próxima vez que alguien acceda ejecutará la nueva versión.

Pero también tiene una desventaja: a cada visita se debe compilar el script. Y es una pérdida de tiempo, ya que si todos los visitantes de tu web ejecutan el mismo script, ¿para qué compilarlo una y otra vez? ¿No sería mejor compilarlo la primera vez, guardar ese script compilado (_opcode_) y ejecutarlo cuando alguien lo solicite?

Pues eso es exactamente lo que hacen los opcode cache: compilan el script la primera vez, lo guardan en memoria (RAM), y cuando se solicita, simplemente ejecutan el script compilado, logrando un aumento de velocidad bastante considerable.

### Me has convencido. ¡Usemos APC!

APC (Another PHP Cache) es un sistema automático que compila los script sólo cuando es necesario. Viene empaquetado como una extensión de PHP, así que su instalación es sencilla. Y más si estamos usando Ubuntu o Debian Linux:
<pre>$ sudo apt-get install php-apc</pre>
Con sólo instalar este paquete (suponiendo que ya tengamos instalados Apache y PHP) ya tendremos APC funcionando correctamente. Si quieres más información sobre la instalación de APC puedes ir a la [documentación oficial](http://php.net/manual/es/book.apc.php) o en Google. Si tu proveedor de hosting no te ofrece APC... lo siento, es lo que tiene el hosting compartido :-P

Para saber si APC está funcionando correctamente, ejecutamos [<kbd>phpinfo()</kbd>](http://php.net/manual/es/function.phpinfo.php):

[![](http://israelviana.es/wp-content/uploads/2013/01/apc.png "Sección de APC en phpinfo()")](http://israelviana.es/wp-content/uploads/2013/01/apc.png)

### APC como cache genérico

Además de "cachear" los scripts, APC sirve como almacenamiento genérico en memoria. Esto quiere decir que puedes guardar datos en la memoria de APC que estarán en RAM (por tanto, será de más rápido acceso que si los guardas en el disco duro o en una base de datos) y que podrás recuperar en cualquier momento. Eso sí, recuerda que si reinicias Apache o el sistema operativo, todos los datos que APC haya guardado en memoria se eliminarán.

Para almacenar una variable en la cache de APC puedes usar la función [<kbd>apc_add()</kbd>](http://es2.php.net/manual/en/function.apc-add.php), y para leerla, [<kbd>apc_fetch()</kbd>](http://es2.php.net/manual/en/function.apc-fetch.php). Por ejemplo:
<pre lang="php">/*
* AVISO: este código tiene fines exclusivamente didácticos, y no debe ser
* utilizado en aplicaciones reales en producción
*/

function leerPerfil() {
$idUsuario = intval($_GET['idUsuario']);

/**
* Lee de la BD los datos del usuario $idUsuario. Si los datos están en cache,
* no hacemos la consulta. Si no están, hacemos la consulta y guardamos los
* datos en cache
*/
if (empty($datosUsuario = apc_fetch("perfil_$idUsuario"))) {
$query = mysql_query("SELECT * FROM usuarios WHERE id = '$idUsuario'");
$datosUsuario = mysql_fetch_assoc($query);
apc_add("perfil_$idUsuario", $datosUsuario);
}

return $datosUsuario;
}</pre>
Este sistema te permite hacer optimizaciones adicionales en tus aplicaciones (por ejemplo, guardar el resultado de una consulta a la base de datos para no tener que hacerla dos veces), pero tendrás que currártelas a mano. Tú eliges qué almacenar en APC. Pero ojo, si vas a hacer un uso intensivo de la caché, es mejor que uses un sistema de caché diferente al de los scripts. Por ejemplo, el famoso [memcached](http://memcached.org/) (usado por sitios como Google o Facebook), que es un sistema de caché genérico, muy eficiente y muy fácil de usar (en serio, es **muy fácil**). Más información sobre [funciones de APC en el manual de PHP](http://es2.php.net/apc).

### Monitorizar APC

Otra de las ventajas de APC es que tiene un precioso panel de control. Es un script PHP al que puedes acceder haciendo un enlace desde tu directorio <kbd>www</kbd> público hasta la ubicación del script (en los directorios del sistema de PHP):

<kbd>gunzip -c /usr/share/doc/php-apc/apc.php.gz &gt; /var/www/apc.php</kbd>

Si no funciona, o si no usas Linux puedes leer la documentación para instalar el panel de control de APC. Si no encuentras el script en tu sistema de ficheros puedes sencillamente [descargarlo desde el Subversion de APC](http://svn.php.net/viewvc/pecl/apc/trunk/apc.php?view=markup).

**Aviso de seguridad:** este script permite ver el estado de APC, revelando alguna información sobre el servidor que puede ser sensible por temas de seguridad. Es tu responsabilidad proteger el acceso al panel de control a través de configuraciones del servidor web, etc.

El panel de control tiene algunas opciones "públicas" y otras a las que sólo puedes acceder iniciando sesión con nombre de usuario y contraseña. Para cambiar el nombre de usuario y la clave puedes editar el propio script del panel de control, apc.php:

[![Cambiar la contraseña al panel de control de APC](http://israelviana.es/wp-content/uploads/2013/01/clave-apc.png "Cambiar la contraseña al panel de control de APC")](http://israelviana.es/wp-content/uploads/2013/01/clave-apc.png)

El panel de control nos ofrece tres apartados:

1.  View Host Stats, donde tenemos un cuadro de control de APC con gráficas de consumo de memoria, información del sistema y datos de uso del cache.
2.  System Cache Entries: la lista de scripts cacheados, así como el número de hits (ejecuciones) que han tenido, su tamaño y fechas de último acceso/última modificación/creación.
3.  User Cache Entries: las variables que se hayan guardado en APC.
[![Configurar APC: panel de control](http://israelviana.es/wp-content/uploads/2012/04/apc-cp-598_.jpg "Configurar APC: panel de control")](http://israelviana.es/wp-content/uploads/2012/04/apc-cp-598_.jpg)

En el próximo artículo explicaremos más detalles sobre el panel de control de PHP y cómo interpretar los datos que nos brinda de cara optimizar aún más nuestro sitio web. Aunque si has llegado hasta aquí y has instalado APC en tu servidor ya has conseguido un gran ahorro de recursos en tu servidor :-)