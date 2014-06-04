Relanzando Caminayven, otra historia de éxito con WordPress
===========================================================

_01-02-2010_

[![Caminayven](http://israelviana.es/wp-content/uploads/2010/02/caminayven-150x150.png)](http://israelviana.es/wp-content/uploads/2010/02/caminayven.png)

Como comentaba en el artículo _"[Primeras experiencias con Wordpress... y muchas nueces ;-)](http://www.israelviana.es/blog/Post/78/primeras-experiencias-con-wordpress-y-muchas/)"_, me estoy introduciendo en el mundillo de WordPress. Nueces San Ignacio no ha sido el único proyecto que he realizado con este gestor de contenidos. Hoy quiero contarte mi experiencia en otro proyecto un poco más grande: **[Caminayven](http://www.caminayven.com)**. Se trata de una revista católica on-line, que lleva más de cinco años on-line sobre [PHP-Nuke](http://phpnuke.org/) con más de mil artículos, un montón de redactores y, afortunadamente, un número creciente de visitas.

Hace dos años que empecé a trabajar en un nuevo gestor de contenidos para Caminayven. Empecé programando desde cero un CMS bastante ambicioso, y aproveché para hacer mis primeros pinitos con JavaScript-RPC (una especie de alternativa a AJAX para comunicación asíncrona, basada en carga dinámica de scripts e iframes). El resultado fue desastroso, aunque aprendí la valiosa lección de que no es bueno reinventar la rueda.

Pero cabezón de mí volví a la carga a principios del año pasado ayudado por [CakePHP](http://cakephp.org/), un framework que me facilitó bastante las cosas, pero que seguía sin ser suficiente. Por falta de tiempo e ilusión el proyecto volvió a quedar abandonado, hasta que hace unos pocos meses empecé a preguntarme si WordPress cubriría las necesidades básicas de Caminayven: publicación de artículos, galerías de fotos, acceso sencillo y elegante a la información (archivos, destacados, relacionados...) y poco más.

De nuevo, un análisis realista y concreto de la Arquitectura de la Información para el proyecto puso las cosas en su lugar. ¿Para qué programar una gestión de usuarios, workflow, editor WYSIWYG, feed RSS, categorías y tags... si Wordpress ya lo hace? Dicho y hecho: convertir las antiguas plantillas de CakePHP en un theme para WordPress fue cuestión de dos tardes (copiando y modificando [Kubrick](http://binarybonsai.com/kubrick/)).

Aunque he tenido algunos problemillas en el _theming_, quizá causados por mi ignorancia sobre WordPress o por su estructura interna en mi opinión poco elegante: el formato de las fechas y la internacionalización. El formato de las fechas, aunque se especifica en el _dashboard_ no se respetaba en el tema Kubrick, además de que los nombres de los meses no se traducen. Con respecto a la internacionalización (más concretamente la traducción), utilizo la [versión en castellano de WordPress](http://es.wordpress.org/), pero aun así muchos mensajes están sin traducir, por lo que he tenido que traducir manualmente algunos fragmentos del theme.

En lo que respecta a la funcionalidad, he utilizado el plug-in Author Image y un código propio en el theme para mostrar, a la derecha de cada artículo, una breve ficha de su autor.

### Galería

Caminayven también destaca por sus reportajes gráficos, la mayoría de ellos realizados por nuestro querido [Javier Cebreros](http://www.flickr.com/photos/elrincondejaviercebreros/). Para publicarlos probé en primera instancia [Awsom PixGallery](http://www.awsom.org/awsom-pixgallery/). Aunque era sencillo utilizarlo y bonito ver una página para cada álbum/foto, el rendimiento es horrible (regenera continuamente las miniaturas), el código es patético (un único y monolítico script de miles de líneas, mezclando PHP y HTML) y el manejo de URL fallaba como una escopeta de feria. Ahora utilizamos [NextGEN Gallery](http://nextgen-gallery.com/), que es justamente lo contrario: un buen rendimiento, mucha flexibilidad a la hora de subir fotos (subida múltiple por HTTP, FTP, etc) y una forma de mostrar los álbumes muy chula. Una posible mejora a este plug-in sería la construcción de colecciones a partir de álbumes en Flickr o Facebook (en Caminayven alojamos algunas fotos en estas redes sociales), y lo añado a mi lista de cosas-que-haré-algún-día.

### Otros plug-ins

También he añadido [WP-Cache](http://mnm.uib.es/gallir/wp-cache-2/) y [All-in-One-SEO-Pack](http://semperfiwebdesign.com/) para optimizaciones de rendimiento y SEO, respectivamente. Ambos funcionando muy bien y sin problemas. WP-Cache y los consejos de [YSlow](http://developer.yahoo.com/yslow/) me han ayudado a hacer Caminayven más rápida, y aún se puede optimizar mucho más.

### Migración

Hasta aquí la parte bonita. Pero no hay que olvidar que Caminayven es un proyecto de cierta envergadura y con una trayectoria de cinco años sobre PHP-Nuke, un gestor de contenidos que odio por ser el perfecto compendio de malas prácticas (de hecho, hace dos años nos colaron una [inyección SQL](https://www.underground.org.mx/index.php?topic=20566.0) que permitió a un [niñato](http://foro.elhacker.net/profiles/naphack-u276104.html) destrozarnos la página...). Así que la migración no se plantea fácil. Por una parte, los artículos se escribieron desde el editor WYSIWYG de Internet Explorer y desde Word, así que te puedes hacer una idea de la impeorable calidad del marcado HTML. No se puede copiar y pegar el contenido de una tabla de PHP-Nuke en otra de WordPress, así que la solución aparente está filtrando el HTML con unas decenas de expresiones regulares caseras y las funciones de inserción del API de WordPress. Además, las categorías han cambiado, y he tenido que hacer una tabla de "traducción" de las viejas a las nuevas categorías.

Y, por si fuera poco, nuestros artículos salen en Google News y perder las antiguas URL (modules.php...) sería desastroso para nuestro posicionamiento, tanto en Google News como en los buscadores en general. Así que ha habido que hacer [redirecciones 301](http://google.dirson.com/posicionamiento.net/redireccion-301/) e intentar encontrar el artículo por su antiguo ID... también he tenido que añadir el ID de los artículos a las URL, por exigencia de Google News (un poco caprichoso, sí).

### Analítica y redes sociales

Al igual que con Nueces San Ignacio, mi trabajo en este proyecto ha ido más allá de lo puramente ingenieril, ya que he diseñado, redactado y otras actividades. Las dos más importantes para el éxito del proyecto han sido la analítica web y la promoción en redes sociales. En cuanto a lo primero, Google Analytics es una gran herramienta pero se está quedando corta para alguna información que necesito saber (los referer exactos cuando un usuario encuentra un error 404 o cosas así), así que estoy introduciendo pequeños códigos de seguimiento propios en la parte del servidor.

Con respecto a las redes sociales, hemos redefinido nuestra estrategia en Facebook, pasando de grupo a [usuario corriente](http://www.facebook.com/caminayven). El motivo primario es la visibilidad, ya que todas las novedades que publiquemos con el usuario Caminayven aparecerán en el timeline de nuestros amigos. En cuanto al contenido, enlazamos los artículos más destacables en el status, publicamos todos los posts con [NetworkedBlogs](http://networkedblogs.com/) y subimos los reportajes gráficos. La estrategia está funcionando en parte, ya que las visitas desde esta fuente han aumentado, pero nuestros seguidores (alrededor de 200) no son muy activos corriendo la voz... es decir, que la [regla del 90-90-1](http://www.dreig.eu/caparazon/2009/01/10/distribucion-desigual-de-la-participacion-en-blogs-comunidades-redes-sociales-etc-90-9-1-algunas-claves/) no se cumple del todo.

También nos hemos dado de alta en Twitter ([@caminayven](http://twitter.com/caminayven)), aunque todavía no hay mucha actividad. Esperemos que en los próximos meses la cosa vaya aumentando.

### Conclusiones

*   WordPress mola. No es la panacea, pero mola.
*   Hacer una revista católica en internet no es fácil, y crear una comunidad alrededor de la misma mucho menos, pero estamos en el camino.
*   Caminayven es un proyecto apasionante al que siempre se le puede sacar punta, quedan muchas cosas por hacer, pero de momento he cumplido con mi deber ;-)
*   La audiencia está respondiendo: las visitas, en general, han aumentado, y en particular, el promedio de páginas por visita. Pero, sobre todo, los comentarios, ese elemento mágico y maravilloso que da vida a un sitio web :-)
En fin, otro proyecto cerrado por el momento... dentro de un mes te cuento el siguiente... ¡más ambicioso y emocionante todavía!.