MagiSQL ha muerto
=================

_07-08-2009_

Hace cerca de un año que comencé un proyecto llamado [MagiSQL](http://sourceforge.net/projects/magisql/), con el objetivo de crear una herramienta modular de gestión de bases de datos relacionales, compatible con múltiples sistemas de BBDD y con licencia libre, por supuesto.

Me he planteado muchas veces la viabilidad y utilidad del proyecto, ya que la competencia es muy dura (phpMyAdmin, pgAdmin, Oracle Enterprise Manager, etc etc etc) y exige reinventar la rueda en la mayoría de los módulos. Además, para que el proyecto fuese exitoso no sólo era necesario que me dejase los ojos durante muchas horas, sino que más programadores se sumasen al proyecto y desarrollasen módulos compatibles con los sistemas gestores de bases de datos, ya que yo no tengo tiempo ni medios para hacer MagiSQL compatible con Oracle, MS-SQL Server, PostgreSQL, MySQL y DB2, como está planteado en los objetivos del proyecto (de momento sólo funcionaba con MySQL). Además, este verano he reescrito por completo el núcleo, lo que ha retrasado y complicado aún más el desarrollo.

Por estos motivos y muchos otros (baja productividad, falta de tiempo y concentración, cuestiones personales...) he decidido no continuar el proyecto. Nunca ha alcanzado un nivel de madurez mínimo como para ser usable, y la gran parafernalia de módulos, clases y patrones hace bastante inútil el código que hay en mi disco duro y en [el SVN de Sourceforge](http://magisql.svn.sourceforge.net/viewvc/magisql/). En resumen, que se irá todo a la basura.

¿Todo? Eso significaría tiempo perdido, derrota, fracaso. Y aunque no deja de serlo, es un fracaso del que estoy orgulloso y del que he aprendido un montón, entre otras cosas:

*   He ideado el patrón de diseño "Application Status", del que tendréis más noticias dentro de algún tiempo... ;-)
*   Me he acostumbrado a escribir documentación en línea ([phpDoc](http://www.phpdoc.org)) y en inglés.
*   Me he familiarizado con la biblioteca [AdoDB](http://adodb.sourceforge.net/).
*   Me he hartado de [Smarty](http://www.israelviana.es/blog/Post/40/plantillas-php-cuestion-de-rendimiento/). Era el último proyecto en el que lo usaba, y desde ahora sólo utilizaré plantillas PHP.
*   He aprendido más cosas sobre la reescritura de URL y expresiones regulares.
*   He comprendido e implementado multitud de patrones.
*   Me he familiarizado con las [excepciones de PHP](http://php.net/manual/es/language.oop5.magic.php) y los [métodos mágicos](http://php.net/manual/es/language.oop5.magic.php).
*   He explotado las [novedades de PHP 5.3](http://www.israelviana.es/blog/Post/48/php-avanza/).
*   He encontrado un buen entorno de programación en PHP sobre Linux (Netbeans, KCacheGrind, etc).
*   Me he convencido una vez más de que reinventar la rueda es friki, divertido pero inútil y bastante frustrante.
*   He escuchado cientos de discos mientras hacía todo eso ;-)
Bueno ¿y ahora, qué? Ya que el proyecto era para una asignatura de la carrera (aprovecho para decir que, tras pasar al Grado en Informática en la UCAM, he tenido que volver a matricularme de una asignatura que ya tenía aprobada), será sustituido por un motor de almacenamiento en MySQL para Object Freezer. [Object Freezer](http://sebastian-bergmann.de/archives/831-Freezing-and-Thawing-PHP-Objects.html) es una biblioteca escrita por Sebastian Bergmann para almacenar objetos en la base de datos [CouchDB](http://couchdb.apache.org/) sin preocuparse de SQL, DAO y demás inventos del pasado ;-)