Patrones de desarrollo web: Multivista
======================================

_12-02-2009_

**Nota: el tratamiento que se le da en este artículo al término "vista" no es el mismo que el que se le da en el patrón MVC.**

Hoy en día, muchos sitios web ofrecen la posibilidad de ver una página no sólo en HTML, sino en PDF, RTF y RSS. Y los webmasters que se quieren subir al carro de la web semántica ofrecen también la información en formato RDF.

Desde el punto de vista del HTML, el navegador interpreta las etiquetas `&lt;link rel="alternate"&gt;` como una vista alternativa del contenido (habitualmente RSS). Desgraciadamente no se usa todo lo que se debería (por ejemplo, las vistas en PDF y RTF que ofrece Joomla! No tienen su `link rel`, sino un enlace normal en la página). Pero ¿y por dentro del sitio web, en el CMS, cómo se implementa de una forma ordenada este modelo multivista? Propongo dos modelos, compatibles entre sí: el primero, más sencillo, basado en los motores de plantillas, y el segundo, basado en la programación orientada a objetos. Este segundo modelo se puede apoyar también en motores de plantillas, y ofrece, además, la posibilidad de implementar programación personalizada para cada plantilla. Vamos a verlo detenidamente.

### Introducción a los motores de plantillas

En la creación de sitios web con motores de plantillas (como por ejemplo Smarty), la idea es separar el código (en nuestro caso PHP) de la interfaz (casi siempre HTML). La manera de usar estos sistemas de plantillas es sencilla: a través de código se extraen (de la base de datos, la entrada del usuario, etc) y manipulan los datos. Los datos se pasan de la forma más atómica posible a la plantilla, y ésta es un documento HTML con una sintaxis especial que permite imprimir el valor de esas variables e implementar una lógica sencilla.

Por ejemplo, este blog se apoya sobre Smarty. El script index.php extrae de la base de datos los últimos post y guarda los datos en un array multidimensional, de la forma:
<pre class="php">$posts = array(

[0] = array (    "titulo" =&gt;   "¿Te entiende Google? Web semántica y PLN",
"texto" =&gt;    "&lt;p&gt;A Google no le interesa entenderme...",
"fecha" =&gt;    "03-02-2009 12:05:30"),

[1] = array (    "titulo" =&gt; "La crisis de la web 2.0",
"texto" =&gt;  "&lt;p&gt;Pensaba escribir un post sobre la crisis...",
"fecha" =&gt;  "31-12-2008 16:55:28")
);

$smarty-&gt;assign("posts", $posts); //Pasa a la plantilla la variable $posts
$smarty-&gt;display("index.tpl"); //Invoca al motor de Smarty para que renderice la plantilla</pre>
Como podrás suponer, el código para obtener el array es
bastante sencillo, ya que la base de datos almacena la información en
un formato muy similar (tablas relacionales). Una vez obtenido, se le pasa la variable `$post` a la plantilla y se renderiza. La plantilla permite ?programar? bucles para que recorra este array de posts, dándole formato a cada uno:
<pre class="php">{foreach $posts as $p}
&lt;div id="post"&gt;
&lt;h1&gt;{$post.titulo}&lt;/h1&gt;
&lt;h3&gt;Publicado a las {$post.fecha} por Isra&lt;/h3&gt;
{$post.texto}
&lt;/div&gt;
{/foreach}</pre>
No es complicado entender la idea. Pongamos por ejemplo que en el blog tengo 4 páginas: index (el índice, que muestra los últimos posts), post (muestra un posts en concreto y sus comentarios), tag (últimos etiquetados con una determinada tag) y archivo (todos los posts de un mes concreto). Por tanto, tendré 4
scripts y 4 plantillas.

### Una vista por cada formato

Pues bien, el patrón Multivista propone tener **un conjunto de las plantillas** (en este caso
index, post, tag y archivo) **por cada vista** que queramos implementar: HTML, RSS, RDF. En el caso del formato PDF no podríamos implementarlo con este sistema, ya que Smarty trabaja sobre texto
plano. Para ello usaremos el segundo modelo (orientado a objetos).

Una buena manera de implementar esto es, en vez de tener un directorio ?templates? con las cuatro plantillas, crear subdirectorios en templates para cada vista, que contuviesen las plantillas:

#### Antes

*   templates/index.tpl
*   templates/post.tpl
*   templates/tag.tpl
*   templates/archivo.tpl

#### Después

*   templates/html/index.tpl
*   templates/html/post.tpl
*   templates/html/tag.tpl
*   templates/html/archivo.tpl
*   templates/rss/index.tpl
*   templates/rss/post.tpl
*   templates/rss/tag.tpl
*   templates/rss/archivo.tpl
*   templates/rdf/index.tpl
*   templates/rdf/post.tpl
*   templates/rdf/tag.tpl
*   templates/rdf/archivo.tpl
Una de las vistas podríamos llamarla "por defecto" (HTML), que se mostraría si una de las plantillas
de la vista requerida faltase. Desde el punto de vista del usuario,
podemos darle la oportunidad de elegir qué vista prefiere, a través
de la URL:

*   http://www.israelviana.es/blog/index.php?vista=html
*   http://www.israelviana.es/blog/index.php?vista=rss
*   http://www.israelviana.es/blog/index.php?vista=rdf
Por supuesto, el usuario no tiene que escribir manualmente la URL, sino se pueden dar enlaces para cambiar
de vista. [img de joomla o plone]

En los tres ejemplos de vista que hemos puesto, todos los formatos tienen la capacidad de enlazarse entre sí: HTML con las etiquetas `&lt;link rel="alternate"&gt;` que ya hemos comentado al comienzo,
RSS con los elementos `link` de `channel` y en RDF con `rdf:about` y otras propiedades.

Este sistema ofrece una metodología sencilla, implementable sin grandes cambios en el CMS y fácilmente
escalable.

El segundo método que veremos, más complejo y potente, lo dejamos para otro día ;-)