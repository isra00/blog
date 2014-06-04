El patrón Strategy y las llamadas dinámicas en PHP
==================================================

_15-06-2009_

Sigo explorando las novedades de PHP 5.3 y, entre ellas están las llamadas dinámicas a métodos estáticos, según he visto en esta [interesante presentación de Ilia Alshanetsky](http://www.scribd.com/doc/4285767/Introduction-to-PHP-53-). Veamos un ejemplo:

<pre lang="php">class Aplicacion {

public static function getVersion() {
return "1.2";
}

}

$metodo = "getVersion";

Aplicacion::$metodo(); //Devuelve "1.2"</pre>

Se ve claramente que podemos llamar a un método estático cuyo nombre no definimos al escribir el código, sino que está en una variable. Esta característica ya estaba disponible en versiones anteriores de la rama PHP 5, tanto para clases como para métodos, pero no estáticos:

<pre lang="php">class Conexion {

public function conectar($servidor, $usuario, $clave) {
echo "Mira Mamá, me estoy conectando!";
}

}

$clase = "Conexion";
$método = "conectar";

$objeto = new $clase();
$objeto->$método("localhost", "pepito", "grill0");
//Imprimirá "Mira Mamá, me estoy conectando!"</pre>

Bueno, y esta característica parece interesante, pero ¿cómo podemos aplicarla a la vida real? Uno de los ejemplos más claros que se me ocurre es la implementación del patrón de diseño Strategy. En este sencillo patrón se trata de cargar una u otra implementación de una clase dependiendo de alguna variable o acción del usuario. Por ejemplo, imaginemos que queremos conectarnos a bases de datos Oracle o MySQL, según qué servicio escoja el usuario desde un formulario. Primero definimos una clase abstracta Conexion que declare los métodos que las implementaciones han de desarrollar, y escribimos sendas clases hijas para Oracle y MySQL:

<pre lang="php">abstract class Conexion {

private $recurso;

public function conectar($usuario, $clave) { }
}

class ConexionOracle {

public function conectar($usuario, $clave) {
$this->recurso = oci_connect($usuario, $clave);
}

}

class ConexionMysql {

public function conectar($usuario, $clave) {
$this->recurso = mysql_connect("localhost", $usuario, $clave);
}

}

/*
* $_POST['dbms'] proviene de un cuadro desplegable (option select) en que se
* da a escoger entre oracle y mysql.
* ucfirst() pone en mayúscula la primera letra
*/

$conector = "Conexion" . ucfirst($_POST['dbms']);
$instancia = new $conector;

try {
$instancia->conectar($_POST['usuario'], $_POST['clave']);
} catch (Exception $e) {
echo "Lo siento, pero por algún motivo la conexión ha fallado";
}
</pre>

En fin, esto es todo, creo que es evidente la potencia de esta característica del lenguaje, que viene a intentar evitar el uso de eval() y dar al lenguaje un poco más de elegancia, al estilo del [<samp>class.forName()</samp> de Java](http://www.j2ee.me/j2se/1.4.2/docs/api/java/lang/Class.html). Por cierto, para una descripción más profunda del patrón Strategy te recomiendo este interesante artículo de [Jack Herrington sobre patrones implementados en PHP](http://www.ibm.com/developerworks/library/os-php-designptrns/).