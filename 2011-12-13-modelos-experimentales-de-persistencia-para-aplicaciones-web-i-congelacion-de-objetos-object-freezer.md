Modelos experimentales de persistencia para aplicaciones web (I): congelación de objetos con Object Freezer
===========================================================================================================

_13-12-2011_

Hoy comienzo una serie de artículos dedicados a exponer diferentes patrones y arquitecturas de software para un problema tradicional: el almacenamiento de datos. Hacer consultas SQL a una base de datos relacional es una de las soluciones más extendidas, pero no es la única. Hay alternativas como las que te voy a presentar en este artículo y los siguientes.

### Disclaimer

**Las ideas que voy a presentar son experimentales. Son conceptos, con su discusón y su implementación (casi siempre parcial), pero no son estándares ampliamente probados y usados en el mundo real. El objetivo de estos artículos es reflexionar, discutir, probar y ver que algunas soluciones pueden ser útiles en ciertos escenarios, y otras quizá no valgan para nada.** :-P

### La congelación de objetos

La primera idea que voy a presentar es un concepto de Sebastian Bergmann. Los datos que se almacenan deben ser objetos de PHP. Se trata de "congelar" los objetos de una forma similar a como lo hacen las funciones de serialización. La diferencia es que el método de congelación _entiende_ las relaciones entre objetos (es decir, que un atributo de un objeto sea, a su vez, otro objeto), de tal forma que si el objeto A tiene una referencia a B y el objeto C tiene una referencia a B también, al _congelar_ los objetos A y C la referencia a B será un puntero al objeto congelado B, en lugar de congelar B dentro de A y B dentro de C, duplicando los datos, como hace [`serialize()`](http://php.net/manual/es/function.serialize.php).

Pero ¿qué quiere decir congelar objetos? Pues sencillamente convertirlos en arrays para poder guardarlos en CouchDB, una base de datos orientada a documentos en la que los datos se almacenan en JSON.

Para comprender el funcionamiento de Object Freezer, nada mejor que un artículo de su autor: [Freezing and Thawing PHP Objects](http://sebastian-bergmann.de/archives/831-Freezing-and-Thawing-PHP-Objects.html).

### ¿Qué problemas soluciona Object Freezer?

1.  La definición y uso de esquemas relacionales. Ya no son necesarias las tablas, claves primarias, foreign keys, etc.
2.  El [mapeo objeto-relacional](http://es.wikipedia.org/wiki/Mapeo_objeto-relacional). No hay que definir la conversión de tablas a objetos y viceversa.
3.  El almacenamiento sencillo de atributos vectoriales: es una forma fácil y automática de guardar arrays en la base de datos.
4.  ¡Tipado dinámico! En PHP se pueden añadir nuevos atributos a un objeto ya existente en tiempo de ejecución. No sería posible guardar este tipo de objetos en esquemas relacionales fijos, pero con Object Freezer es posible.
Además, tiene la ventaja de que, al guardar los objetos en CouchDB se maneja el **versionado** de los objetos, ya que CouchDB [controla los cambios de todos sus registros](http://couchdb.apache.org/docs/overview.html).

### Dame algo de código

<pre class="php">//Una clase cualquiera
class Coche {
private $color = 'rojo';
}

//Una instancia de una clase cualquiera
$ferrari = new Coche();

require_once 'Object/Freezer/Storage/CouchDB.php';

//Instancia el freezer indicando los datos de conexión a CouchDB
$storage = new Object_Freezer_Storage_CouchDB('test', new Object_Freezer, TRUE, 'localhost', 5984);

//Congela el objeto y lo almacena en CouchDB, devolviendo el ID auto-generado
$id = $storage-&gt;store($ferrari);</pre>
Así de sencillo. CouchDB asigna un ID único a cada nuevo registro. Para leer un registro:
<pre class="html">//Devuelve el objeto de tipo Coche con $id
$objeto = $storage-&gt;fetch($id);</pre>

### ¿Es adecuado Object Freezer para mi proyecto?

La decisión de almacenar datos sin un esquema fijo es tan importante como la de usar una base de datos relacional o una NoSQL. ¿Es NoSQL bueno? No vamos a discutirlo aquí, pero sí citaré un tweet que leí una vez:
> Usar NoSQL es como ir sin calzoncillos. Es cómodo, pero peligroso.
No obstante, hay una cuestión a tener en cuenta en la decisión: si usamos Object Freezer es cierto que la base de datos será _schema-less_, pero no es cierto que nuestros datos no estén estructurados, ya que ¡usamos objetos! Sería más correcto decir que los datos no están estructurados **en la base de datos**, pero sí en el código.

Si hemos hecho un buen diseño de las clases y sus relaciones, Object Freezer puede ser una solución rápida y fácil para guardar esos objetos en una base de datos muy interesante como CouchDB.

Sin embargo, no creo que sea buena idea para proyectos...

*   con fuerte necesidad de [integridad referencial](http://es.wikipedia.org/wiki/Integridad_referencial), o
*   que no estructuran toda la información con objetos, o
*   grandes, o
*   si crees que el modelo almacenamiento de datos cambiará, o
*   si no sabes usar las [vistas de CouchDB](http://wiki.apache.org/couchdb/HTTP_view_API) para obtener listas de datos filtradas, indizadas, etc.
Creo que con esto es suficiente introducción. Puedes consultar la presentación que he citado arriba y el [blog de Sebastian Bergmann](http://sebastian-bergmann.de/archives/831-Freezing-and-Thawing-PHP-Objects.html "Artículo de Sebastian en su blog explicando Object Freezer") para tener más información sobre [Object Freezer](https://github.com/sebastianbergmann/php-object-freezer), que por cierto es software libre :-)

Esto es todo por hoy. Ahora **te toca a ti**: ¿qué te parece el concepto de congelar los objetos? ¿Usarías Object Freezer? ¿Usarías CouchDB? ¿Conoces soluciones similares para otros lenguajes de programación?