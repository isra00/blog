Relacionando enlaces en Delicious y contenidos en el blog
=========================================================

_04-04-2009_

![Enlazando enlaces en Delicious y contenidos en el blog](/wp-content/uploads/image-legacy/blog-delicious.gif)

Hoy vamos a ver una aplicación experimental de eso que llaman "linked data", una tendencia que podría considerarse web semántica, aunque sin ontologías ni estándares, sino basada en RSS, servicios web y API específicas para cada servicio: se trata de **obtener enlaces en Delicious relacionados con los contenidos del blog**.

Lo que hace este sencillo algoritmo es comparar las "nubes" de tags: obtiene los últimos enlaces de una cuenta de Delicious, comprueba que contengan las tags más recurrentes en el blog, y, cuanto más concurrentes sean las tags en el blog, más puntuación de afinidad se le da al enlace.

El ejemplo en acción lo puedes ver ahí a la derecha. Las tags más recurrentes del blog (web semántica, web social, evolución de internet...) están presentes en los enlaces.

El algoritmo puede ser útil para saber cómo evolucionan los intereses de redacción y navegación del blogger o aportar contenido añadido al blog. Estoy preparando más artículos con más criterios para evaluar la &quot;cercanía temática&quot; de los artículos, basándose en las etiquetas.

El código de abajo funciona sobre el gestor de contenidos de este sitio. Si a alguien le interesa hacer un plug-in para WordPress que soporte esta funcionalidad lo podemos intentar. Habría que adaptar la obtención de los datos (adaptando las sentencias SQL a la BD de WordPress o sustituyéndolas por llamadas a la API) y el modo de mostrarlos (sin Smarty).

### Función para ordenar los enlaces

//Ordena una matriz por el valor de una de sus claves (primer nivel)

//TODO parece que no funciona bien

function array_sort_clave(&amp;$data, $campo) {

&nbsp;&nbsp;//Crea una función personalizada para el campo

&nbsp;&nbsp;$codigo_funcion = "return strcmp($a['$campo'], $b['$campo']);";

&nbsp;&nbsp;usort($data, create_function('$a,$b', $codigo_funcion));

&nbsp;&nbsp;return $data;

}
</pre>

### Clase Enlace (la utilizo para los enlaces de Delicious y para el blogroll)

class Enlace {

&nbsp;&nbsp;public $titulo;

&nbsp;&nbsp;public $url;

&nbsp;&nbsp;public $icono;

&nbsp;&nbsp;public $rel;

&nbsp;&nbsp;public $tags = array();

&nbsp;&nbsp;

&nbsp;&nbsp;public function __construct($titulo, $url, $icono, $rel=null, $tags=null) {

&nbsp;&nbsp;&nbsp;&nbsp;$this->titulo = $titulo;

&nbsp;&nbsp;&nbsp;&nbsp;$this->url = $url;

&nbsp;&nbsp;&nbsp;&nbsp;$this->icono = $icono;

&nbsp;&nbsp;&nbsp;&nbsp;$this->rel = $rel;

&nbsp;&nbsp;&nbsp;&nbsp;$this->tags = $tags;

&nbsp;&nbsp;}

}
</pre>

### La &quot;chicha&quot;

//Obtiene una nube de tags correspondiente a todos los post

static public function getTagcloudSimple() {

&nbsp;&nbsp;$tagcloud = array();

&nbsp;&nbsp;$cTags = BDConsulta("SELECT tag FROM tags WHERE post IN (SELECT id FROM posts ORDER BY creado DESC)");

&nbsp;&nbsp;while ($tag = recorrer_resultados($cTags)) {

&nbsp;&nbsp;&nbsp;&nbsp;$tagcloud[$tag['tag']]++;

&nbsp;&nbsp;}

&nbsp;&nbsp;return $tagcloud;

}

//Obtiene enlaces de Delicious, en orden de afinidad con el blog

//La afinidad con el blog se calcula a partir de las tags de los posts del blog. A cada enlace se le asigna un índice de afinidad

//El índice de afinidad se calcula sumando las veces que cada tag del enlace aparece en el blog

static public function getDelicious() {

&nbsp;&nbsp;$enlaces = self::getEnlacesDelicious();

&nbsp;&nbsp;$enlaces_con_puntos = array();

&nbsp;&nbsp;$tags_blog = self::getTagcloudSimple();

&nbsp;&nbsp;foreach ($enlaces as $enlace) {

&nbsp;&nbsp;&nbsp;&nbsp;$puntos = 0;

&nbsp;&nbsp;&nbsp;&nbsp;foreach ($enlace->tags as $tag_del_enlace) {

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$puntos += $tags_blog[$tag_del_enlace];

&nbsp;&nbsp;&nbsp;&nbsp;}

&nbsp;&nbsp;&nbsp;&nbsp;$enlaces_con_puntos[] = array('datos'=>$enlace, 'puntos'=>$puntos);

&nbsp;&nbsp;}

&nbsp;&nbsp;array_sort_clave($enlaces_con_puntos, "puntos");

&nbsp;&nbsp;$enlaces_final = array();

&nbsp;&nbsp;$cnt = count($enlaces_con_puntos);

&nbsp;&nbsp;//Ordena inversamente la matriz, aunque ordena sólamente las claves

&nbsp;&nbsp;for ($i=0; $i&lt;$cnt; $i++) {

&nbsp;&nbsp;&nbsp;&nbsp;$enlaces_final[$cnt-1-$i] = $enlaces_con_puntos[$i];

&nbsp;&nbsp;}

&nbsp;&nbsp;//Ordena el array según las nuevas claves. Vaya lío...

&nbsp;&nbsp;ksort($enlaces_final);

&nbsp;&nbsp;return $enlaces_final;

}

&nbsp;&nbsp;

//Se procesa el RSS de los enlaces y se crea un array de objetos Enlace con él

static public function getEnlacesDelicious($limite=30) {

&nbsp;&nbsp;if (!is_int($limite)) trigger_error("El parámetro debe ser un entero", E_USER_ERROR);

&nbsp;&nbsp;//$xml = simplexml_load_file(RUTA_L . "delicious.xml");

&nbsp;&nbsp;$xml = simplexml_load_file("http://feeds.delicious.com/v2/rss/isra00?count=$limite");

&nbsp;&nbsp;$enlaces = array();

&nbsp;&nbsp;foreach ($xml->channel->item as $item) {

&nbsp;&nbsp;&nbsp;&nbsp;$tags_item = array();

&nbsp;&nbsp;&nbsp;&nbsp;//No vale pasar $item->category directamente al constructor de Enlace, mejor pasarlo a array de cadenas

&nbsp;&nbsp;&nbsp;&nbsp;foreach ($item->category as $s) {

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$tags_item[] = utf8_decode((string) $s);

&nbsp;&nbsp;&nbsp;&nbsp;}

&nbsp;&nbsp;&nbsp;&nbsp;$enlaces[] = new Enlace(utf8_decode($item->title), utf8_decode($item->link), null, null, $tags_item);

&nbsp;&nbsp;}

&nbsp;&nbsp;

&nbsp;&nbsp;return $enlaces;

}
</pre>

### Mostramos los enlaces en una plantilla Smarty

&lt;h3&gt;Enlaces Delicious&lt;/h3&gt;
&lt;div id=&quot;blogroll&quot; class=&quot;sb_seccion pq&quot;&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&lt;p&gt;Mostrando enlaces relacionados con el blog. &lt;a href=&quot;http://delicious.com/isra00/&quot;&gt;Ver todos los enlaces&lt;/a&gt;.&lt;/p&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&lt;ul&gt;

&nbsp;&nbsp;&nbsp;&nbsp;{foreach from=$delicious item=enlace name=i}{if $smarty.foreach.i.index &lt; 10}

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;li&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;a href=&quot;{$enlace.datos-&gt;url}&quot;&gt;{$enlace.datos-&gt;titulo}&lt;/a&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{foreach from=$enlace.datos-&gt;tags item=tag}

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;a class=&quot;insignificante&quot; href=&quot;http://delicious.com/isra00/{$tag}&quot;&gt;[{$tag}]&lt;/a&gt;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{/foreach}

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;/li&gt;

&nbsp;&nbsp;&nbsp;&nbsp;{/if}{/foreach}

&nbsp;&nbsp;&nbsp;&nbsp;&lt;/ul&gt;

&lt;/div&gt;
</pre>