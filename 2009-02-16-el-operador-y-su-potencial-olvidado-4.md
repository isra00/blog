El operador # y su potencial olvidado
=====================================

_16-02-2009_

El carácter # se añade al final de una URL para indicar un fragmento o ancla en una página web. Así lo deja caer el [RFC 1738](http://www.ietf.org/rfc/rfc1738.txt) (página 2), la especificación oficial del protocolo URL:

> The character "#" is unsafe and should always be encoded because it is used in World Wide Web and in other systems to delimit a URL from a fragment/anchor identifier that might follow it.

Esta es la referencia más clara al operador # que se hace en el documento, y, como se puede ver, no especifica cuál ni cómo debe ser el uso del operador, sino que simplemente nombra el uso que se le está dando, ya que el párrafo explica la codificación de caracteres.

Si buscamos información en fuentes no tan "oficiosas", la [Wikipedia](http://es.wikipedia.org/wiki/Direcci%C3%B3n_de_Internet) nos dice que una URL tiene la siguiente estructura:

> <pre>esquema://autoridad/ruta?consulta#**fragmento**</pre>
> El **fragmento** identifica a una porción de un recurso, habitualmente una ubicación en un documento.

Nada nuevo de momento. Pero la enciclopedia colaborativa añade:

> La parte #enlace, por último, es conocida como identificador de fragmento y se refiere a ciertos lugares significativos dentro de una página; por ejemplo, esta página tiene enlaces internos hacia cada cabecera de sección a la cual se puede dirigir usando el ID de fragmento. Esto es relevante cuando un URL de una página ya cargada en un navegador permite saltar a cierto punto en una página extensa.

Y también interesante:

> Dado que el protocolo HTTP permite que un servidor responda a una solicitud redireccionando el navegador web a un URL diferente, muchos servidores adicionalmente permiten a los usuarios omitir ciertas partes del URL, tales como la parte "www.", o el carácter numeral ("#") de rastreo si el recurso en cuestión es un directorio. Sin embargo, estas omisiones técnicamente constituyen un URL diferente, de modo que el navegador web no puede hacer estos ajustes, y tiene que confiar en que el servidor responderá con una redirección. Es posible para un servidor web (pero debido a una extraña tradición) ofrecer dos páginas diferentes para URL que difieren únicamente en un carácter "#".

En conclusión, el uso del operador # no está regulado ni normalizado, y es un estándar de facto únicamente en el protocolo HTTP, donde se le da un uso concreto: saltar a un marcador dentro de la propia página. Para ello está la etiqueta HTML <pre>&lt;a name=&quot;nombre_marcador&quot;&gt;&lt;/a&gt;</pre>, aunque el comportamiento se implementa en el navegador, por lo que no es obligatoria otra llamada al servidor. No obstante, los servidores web pueden modificar ligeramente su comportamiento.

### Hackeando el operador en AJAX

Hasta hace poco, éste era todo el uso que se le daba al marginado operador #: saltar a otros "marcadores" internos dentro de una página web. Hoy en día, el operador # se utiliza en combinación con JavaScript en interfaces web basadas en AJAX, donde en una sóla página (es decir, sin cambiar la parte <kbd>esquema://autoridad/ruta?consulta</kbd> de la URL) se producen múltiples acciones. Y se utiliza precisamente para cambiar la URL aunque el navegador realmente no recarge la página entera, consiguiente así dos interesantes detalles relacionados con la usabilidad:

*   La posibilidad de usar las funciones "Atrás" y "Adelante" del navegador.
*   La posibilidad de identificar inequívocamente una sección de la página y poder enlazarla o guardarla como favorita.

Hoy en día este comportamiento se puede comprobar en sitios como GMail o Facebook. Por ejemplo, si entramos en <pre>http://www.facebook.com/home.php</pre>, tenemos la página principal. Si queremos subir un vídeo, pulsamos el enlace correspondiente, el cual cargará la interfaz a través de AJAX. Si quisiéramos enviarle a un amigo la dirección para subir vídeos en Facebook, tendríamos que indicarle que fuese a <pre>http://www.facebook.com/home.php</pre> y, una vez allí, pulsar en "Subir vídeo". Gracias al operador #, no es necesario, ya que el enlace que pulsamos "Subir vídeo", además de contener un atributo onclick que cargue la interfaz de subida de vídeos a través de AJAX, es un enlace que apunta al marcador <pre>#/video/?upload</pre>, obteniendo así la URL <pre>http://www.facebook.com/home.php#/video/?upload</pre> sin haber prescindido de AJAX ni haber recargado al completo la página.

### ¿Hay vida más allá de HTML? I have a dream...

Como ya se ha indicado, el uso del operador # en las URL se reduce prácticamente a HTML. Y es lógico, ya que su uso no está normalizado y su utilidad en HTML es bastante reducida. En una web pensada para una lectura natural (humana), saltar a diferentes partes de la página nos puede resultar cómodo, pero no es muy útil desde el punto de vista puramente tecnológico.

Más allá de esta aplicación, las tecnologías semánticas, concretamente RDF, hacen uso extensivo de las URL (mejor, dicho de las [URI](http://es.wikipedia.org/wiki/Uniform_Resource_Identifier)) y del operador #. Un ejemplo es este encabezado [RDF](http://es.wikipedia.org/wiki/Resource_Description_Framework) en el que se especifican los [espacios de nombres XML](http://es.wikipedia.org/wiki/Espacios_de_nombres_XML) que se van a usar en la descripción:

<pre>&lt;rdf:RDF
xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
xmlns:cd="http://www.recshop.fake/cd#"&gt;</pre>

En este caso, el operador # no especifica ningún fragmento o ancla en los documentos referenciados. ¿Qué diferencia hay entre usarlo y no usarlo? Si probamos ambas opciones obteniendo por HTTP el documento (navegadores, wget, etc) el resultado es el mismo. Lo mismo ocurre con <pre>http://johnbreslin.com/blog/index.php?sioc_type=site#weblog</pre>, otra URL de [ejemplo](http://rdfs.org/sioc/spec/). Está claro que, a nivel de navegador no se está haciendo ninguna modificación del documento solicitado, como cambría imaginarse en un contexto de XML.

Pensémoslo. Sería realmente _jugoso_ poder obtener fragmentos de un documento mediante este operador. La obtención del fragmento se podría producir en el servidor o en el cliente. Si hablamos de documentos XML, los fragmentos podrían estar expresados en [XPath](). Por ejemplo, supongamos un documento XML tal que:

<pre>&lt;?xml version="1.0" ?&gt;
&lt;libros&gt;
&lt;libro&gt;
&lt;titulo&gt;El manifiesto cluetrain&lt;/titulo&gt;
&lt;autor&gt;Rick Levine&lt;/autor&gt;
&lt;autor&gt;Christopher Locke&lt;/autor&gt;
&lt;autor&gt;Doc Searls&lt;/autor&gt;
&lt;autor&gt;David Weinberger&lt;/autor&gt;
&lt;isbn&gt;978-84-234-2693-5&lt;/isbn&gt;
&lt;/libro&gt;
&lt;libro&gt;
&lt;titulo&gt;Organízate con eficacia&lt;/titulo&gt;
&lt;categoria&gt;Habilidades directivas&lt;/categoria&gt;
&lt;autor&gt;David Allen&lt;/autor&gt;
&lt;editado&gt;2006&lt;/editado&gt;
&lt;edicion&gt;1&lt;/edicion&gt;
&lt;/libro&gt;
&lt;/libros&gt;</pre>

...identificado por la URL <pre>http://www.israelviana.es/libros.xml</pre>. Sería fantástico poder obtener, a través de la dirección <pre>http://www.israelviana.es/libros.xml#/libros/libro/autor</pre>, una lista de autores. Este sistema podría complementar o incluso sustituir, en algunos casos, a los actuales servicios web, basados en XML y métodos remotos para obtener datos.

En fin, por imaginar que no quede...