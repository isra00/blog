El curioso método getDocComment()
=================================

_29-05-2009_

Uno de los métodos de la clase [ReflectionFunction](http://php.net/manual/es/class.reflectionfunction.php) del API de reflexión de PHP se llama getDocComment() y devuelve los comentarios de documentación de una función o método. Por ejemplo:
<pre lang="php">/**
* @param   int  $a Primer sumando
* @param   int  $b Segundo sumando
* @return  int  Suma de $a + $b
*/
function suma($a, $b) {
//Otro comentario
return $a + $b;
}

$funcion = new ReflectionFunction("suma");
echo $funcion-&gt;getDocComment() . "n";</pre>
El código anterior devolverá el comentario completo:
<pre><kbd>isra@isra:~$ php -f reflexion.php /** * @param int $a Primer sumando * @param int $b Segundo sumando * @return int Suma de $a + $b */ </kbd></pre>
Hay que recalcar que no devuelve todos los comentarios, sino sólo los de documentación. Por eso si olvidamos poner dos asteriscos al inicio, no devolverá nada:
<pre><kbd>isra@isra:~$ php -f reflexion.php</kbd></pre>
Tampoco devolverá nada si utilizamos el símbolo de comentario de una línea //:
<pre lang="php">// @param   int  $a Primer sumando
// @param   int  $b Segundo sumando
// @return  int  Suma de $a + $b
function suma($a, $b) {
//Otro comentario
return $a + $b;
}

$funcion = new ReflectionFunction("suma");
echo $funcion-&gt;getDocComment() . "n";</pre>
<pre><kbd>isra@isra:~$ php -f reflexion.php</kbd></pre>
En fin, un método interesante que puede servir para simplificar y aumentar el rendimiento de los sistemas de documentación como phpDocumentator, y también puede ser útil en el control de errores, combinándolo con demás métodos de ReflectionFunction.