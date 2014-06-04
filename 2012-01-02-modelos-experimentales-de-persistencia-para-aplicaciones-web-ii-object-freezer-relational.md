Modelos experimentales de persistencia para aplicaciones web (II): Object Freezer-Relational
============================================================================================

_02-01-2012_

En el [anterior post](http://israelviana.es/modelos-experimentales-de-persistencia-para-aplicaciones-web-i-congelacion-de-objetos-object-freezer/) vimos un invento de Sebastian Bergmann llamado Object Freezer, que consiste en almacenar objetos PHP en CouchDB de forma automática. Es un buen método para no tener que definir esquemas de bases de datos, escribir sentencias SQL, hacer mapeo objeto-relacional... en definitiva, es una solución que nos puede ahorrar un montón de trabajo.

Pero, una vez aceptadas las ventajas de la congelación, quizá sea necesario guardar esos objetos en una base de datos diferente a CouchDB. Por ejemplo, en MySQL. Y de esto trata el presente artículo: de congelar objetos y guardarlos en MySQL.

### Un brevísimo repaso a la congelación

Congelar un objeto ("freeze") es convertirlo en un array manteniendo los objetos hijos y evitando duplicidad de los mismos. La operación inversa, la descongelación ("thaw"), sería recoger ese array y convertirlo de nuevo en el objeto original.

### ¿Por qué MySQL, si con NoSQL nos ahorramos todo el trabajo?

1.  A veces no es viable o rentable cambiar de servidor de bases de datos, por la inversión económica y de formación que requieren los sistemas tradicionales.
2.  Interoperabilidad: si en el futuro queremos migrar la aplicación, quizá poder manipular la información con SQL facilite la tarea.
3.  Rendimiento: las bases de datos relacionales se basan en algoritmos muy evolucionados y rápidos, y sistemas como MySQL-MyISAM han demostrado un rendimiento muy alto.

### Entonces... ¿Se trata de guardar arrays en MySQL?

Exacto. Eso es lo que hace [Object Freezer-Relational](http://objectfreezer-r.sourceforge.net/), una extensión de Object Freezer escrita por un servidor :-)

Lo primero que debemos pensar a la hora de guardar objetos en MySQL es: ¿cuál es el esquema de tablas? Porque si con CouchDB éramos NoSQL y guardábamos la información en arrays JSON, ahora tenemos que ceñirnos al álgebra relacional, las tablas, los índices y las claves primarias.

Así que este es el esquema que podemos usar:

*   Tabla **objects**

*   **id**: el ID único del objeto.
*   **className**: indica la clase de la cual es instancia el objeto almacenado.
*   **isDirty** y **isRoot: **dos atributos que utiliza Object Freezer internamente.*   Tabla **properties**
Llegados a este punto uno podría pensar: "hey, te estás cargando el modelo relacional". Efectivamente. Si queremos guardar objetos sin definir su esquema de BD, no nos queda más remedio que soluciones experimentales como la que te presento hoy. Sí, es una des-normalización, pero gracias a ella te ahorras escribir SQL.

Object Freezer-Relational se encarga de gestionar este esquema. De hecho, no es siquiera necesario crearlo, ya que lo hace automáticamente.

### Vale, ya veo que mola. ¿cómo se usa?

Usar Object Freezer-Relational es tan sencillo como usar Object Freezer indicando los datos de acceso a MySQL:

```php
$storage = new Object_Freezer_RelationalStorage(
new Object_Freezer,
NULL,
FALSE,
new MysqlStorage(
"localhost",                    //Servidor MySQL
"freezer",                      //Usuario
"passw0rd",                     //Contraseña
"freezer",                      //Base de datos
3306,                           //Puerto
MysqlStorage::ENGINE_INNODB));  //Motor MySQL
```

A partir de ahí, podemos usar el objeto `$storage` de la misma forma que el Object Freezer original, con CouchDB, ya que es la misma API.

Object Freezer-Relational está disponible en SourceForge. Puedes [descargarlo](https://sourceforge.net/projects/objectfreezer-r/files/) y probarlo, echar un vistazo al [código](https://sourceforge.net/scm/?type=svn&amp;group_id=275421 "Repositorio Subversion de ObjectFreezer-Relational") o leer la [documentación](http://objectfreezer-r.sourceforge.net) si te interesa. Y por supuesto, comentar qué te parece la idea de guardar objetos en una base de datos MySQL :-)
