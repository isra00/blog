Patrones de desarrollo web: Multivista (II)
===========================================

_14-05-2009_

Aquí tienes la procrastinada segunda parte del [patrón Multivista](http://www.israelviana.es/blog/Post/30/patrones-de-desarrollo-web-multivista/). En este post veremos cómo añadir lógica a las vistas y cuáles son los límites de este patrón. Como caso práctico del Multivista orientado a objetos tendremos este mismo blog.

### ¿Cuándo es necesario este segundo método?

*   Cuando hay plantillas sin salida de texto: PDF, imágenes, flash...
*   Cuando se utilizan helpers o librerías para generar documentos: Pear-Html, DOMDocument, etc
*   Cuando no se quiere emplear un sistema de plantillas.
El patrón Multivista orientado a objetos es similar al patrón <span style="text-decoration: line-through;">Template</span> [Two Step View](http://www.martinfowler.com/eaaCatalog/twoStepView.html), y se introduce normalmente en una estructura <span style="text-decoration: line-through;">Modelo-Vista-Control</span> de tres capas, como ya expliqué en la primera entrega. Se basa principalmente en la abstracción de las acciones ("postergar los problemas", como dice mi compañero Dors). Si en el primer método cargábamos los mismos datos introduciéndolos en una u otra plantilla, ahora tendremos una abstracción de tres niveles, donde podremos cargar unos datos comunes (ahí suelen estar las llamadas a los DAO) en el primero, ejecutar unas acciones específicas en el segundo, y el tercer nivel (opcional) serían las plantillas. Veámoslo con un ejemplo:
<pre lang="php" escaped="true">class VerPost extends Vista {
    public function cargarDatos() {
        Dao::getPost($_GET['id']);
    }

    public post() {
        //Aquí se opera con los parámetros post, si los hubiese (por ejemplo, registrar un comentario)
    }
}

class VerPostHtml extends VerPost {
    //Esta vista podría haber sido implementada con el 1º método
    public function run() {
        $smarty-&gt;assign("post", $this-&gt;cargarDatos());
        $smarty-&gt;display("verPost.tpl");
    }
}

class VerPostPdf extends VerPost {
    //Esta vista requiere código PHP específico, por lo que el 1º método no valdría
    public function run() {
        header("Content-type: application/pdf");
        $datos = $this-&gt;cargarDatos());
        $pdf = new Pdf();
        $pdf-&gt;addText($datos['titulo']);
        echo $pdf-&gt;flush();
    }
}</pre>
No es complicado entender este método: la parte de la vista se segrega en dos niveles, el primero es una clase común a todas las vistas del módulo y el segundo nivel son las vistas específicas. Pero el Multivista no se queda en este sencillo código. Podemos establecer clases horizontales comunes a todas las vistas específicas de un tipo completo, por ejemplo, las vistas en PDF:
<pre lang="php" escaped="true">class ComunPdf {
    static public function run() {
        //Construye una cabecera común a todas las vistas
        header("Content-type: application/pdf");
        $pdf = new Pdf();
        $pdf-&gt;addText("Blog de Israel Viana");
        $pdf-&gt;addText("www.israelviana.es");
    }
}</pre>
<pre lang="php" escaped="true">class ComunRss {
    static public function run() {
        header("Content-Type: application/xml+rss");
    }
}</pre>

### Clases comunes a vistas del mismo tipo

Quizás queramos tener alguna funcionalidad común para todas las vistas RSS, por ejemplo. Esta funcionalidad se puede implementar de varias maneras: por una parte las propias clases específicas pueden llamar a las transversales:
<pre lang="php" escaped="true">class VerPostPdf extends VerPost {
    public function run() {
        $datos = $this-&gt;cargarDatos());
        VistaPdf::cabecera();
        $pdf-&gt;addText($datos['titulo']);
        echo $pdf-&gt;flush();
        VistaPdf::pie();
    }
}</pre>
O bien puede hacerse desde el motor de vistas:
<pre lang="php" escaped="true">//Instancia la clase que corresponda
$vista = $_GET['vista'];
$plantilla = $_GET['plantilla'];
eval("$v = new $vista$plantilla;");

//Invoca los métodos comunes a la plantilla
eval("Comun$plantilla::run();");

//Ejecuta la plantilla
$v-&gt;run();</pre>

### URL amigables

Siendo ortodoxos, esto no estaría dentro del patrón Multivista en sí, pero puede ser una buena forma de manejar las aplicaciones que funcionen con este patrón: veamos cómo interpretar URL del tipo: http://servidor.net/Accion/Vista/param1/param2/.../paramn/vista. Manejar direcciones con esta estructura tiene varias ventajas:

*   Organizar nuestros módulos de una manera bastante intuitiva, separando los scripts "importantes" para nuestra aplicación y las librerías
*   Poder disponer de un script único de acceso (el dispatcher de URL), que maneje los argumentos, los errores y los ataques de inyección SQL y por el estilo. Es decir, se solicite la URL que se solicite, siempre se ejecutará el dispatcher.
*   Cumplir la recomendación de accesibilidad de Jakob Nielsen de hacer URL "hackeables", para que un usuario pueda cambiar de plantilla sin necesidad de enlaces.
*   Mejorar el posicionamiento en buscadores de nuestro sitio web (SEO).
Lo primero es redirigir todas las peticiones al dispatcher. En Apache se hace así:
<pre>RewriteEngine on RewriteRule .* index.php</pre>
Finalmente, el código del dispatcher tendría esta pinta (el código está bastante simplificado, la versión _real_ la publicaré con el resto del código del blog):
<pre lang="php" escaped="true">//Vistas
$vistas = array(
    'Index'     =&gt; array('nombre'=&gt;"Index",  'plantillas'=&gt;array("Html", "Rss", "Rdf")),
    'Post'      =&gt; array('nombre'=&gt;"Post",       'plantillas'=&gt;array("Html", "Rss", "Rdf")),
    'Archivo'   =&gt; array('nombre'=&gt;"Archivo",    'plantillas'=&gt;array("Html", "Rss", "Rdf")),
    'Pagina'    =&gt; array('nombre'=&gt;"Pagina",     'plantillas'=&gt;array("Html", "Rss", "Rdf")),
    'Proyectos' =&gt; array('nombre'=&gt;"Proyectos", 'plantillas'=&gt;array("Html"))
);

$GLOBALS['vista_por_defecto'] = "Index";
$GLOBALS['plantilla_por_defecto'] = "Html";

$peticion = $_SERVER['REQUEST_URI'];
$parametros = split('/', substr($peticion, strlen(RUTA_R))); //Quita la URL base

//Obtiene el módulo
$v = (strlen($parametros[0])) ? ucfirst($parametros[0]) : $GLOBALS['vista_por_defecto'];

//El módulo especificado no existe
if (!array_key_exists($parametros[0], $vistas)) {
    include("404.php");
    die();
}

//Elimina el módulo de los parámetros
array_shift($parametros);

//Protege contra el error de no incluir barra final en URL que no contienen vista específica, por ejemplo /Post/31/titulo-del-post
$ultimo_parametro = ucfirst($parametros[count($parametros)-1]);
if (array_search($ultimo_parametro, array_merge(array(null), $vistas[$v]['plantillas']))) {
    $p = $ultimo_parametro;
    array_pop($parametros);
} else {
    $p = $GLOBALS['plantilla_por_defecto'];
}

//Verifica si está existe la vista
if (array_key_exists($v, $vistas)) {
    $vista = $vistas[$v];
    //Verifica si existe la plantilla
    //No vale negar con ! porque si el índice es 0, la condición no se cumpliría
    if (null == array_search($p, array_merge(array(null), $vista['plantillas']))) die("La plantilla especificada no existe");
} else {
    die("La sección especificada $v no existe");
}

//Pasa los parámetros a la vista
$GLOBALS['parametros'] = $parametros;
$smarty-&gt;assign("parametros", $GLOBALS['parametros']);

$GLOBALS['vista_actual'] = $v;
$GLOBALS['plantilla_actual'] = $p;

//Carga las clases vista y plantilla
require_once("vistas/Vista$v.php");
require_once("vistas/$v/$v$p.php");

//Instancia la plantilla
eval("$vista = new $v$p();");
//Métodos comunes (transversal) al tipo de plantilla
if (file_exists("vistas/Plantilla$p.php")) include("vistas/Plantilla$p.php");
//Ejecuta la plantilla
$vista-&gt;run();</pre>
Y con esto, un DAO y poco más (veremos en sucesivos artículos qué es lo que falta) tendremos un marco de trabajo para aplicaciones web multivista. No se trataría de un MVC en toda regla, pero sí de una arquitectura de tres capas válida para aplicaciones robustas.