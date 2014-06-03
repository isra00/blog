Tests de integración con Zarangotest
====================================

¿Cuántas veces hemos tenido que pelearnos con los ficheros de configuración a la hora de desplegar nuestro proyecto?

Respuesta: un montón. Y la mayoría de ellas, evitables.

La versión de PHP, las extensiones instaladas, los datos de conexión a la base de datos correctos... son algunas de las comprobaciones que debemos hacer cada vez que desplegamos una nueva versión de la aplicación. Y como es de bien nacidos automatizar los tests, algunos lo hacen con PHPUnit, otros de soluciones caseras... y otros usamos [Zarangotest](https://github.com/isra00/zarangotest).

Zarangotest (cuyo nombre proviene del [zarangollo](http://es.wikipedia.org/wiki/Zarangollo), delicioso plato tradicional murciano) es un sencillísimo framework que podría haber escrito cualquiera, cuyos objetivos son *organizar los tests (primordialmente los de integración), proporcionar una serie de tests ya escritos y presentar los resultados en una bonita página HTML*.

http://israelviana.es/wp-content/uploads/2012/08/zarangotest.gif

¿Cómo se usa Zarangotest?
-------------------------

Zarangotest, a diferencia de otros frameworks de test, está pensado para usar desde el servidor web, no desde la consola.

 1. Cargas el fichero de zarangotest: `require_once 'zarangotest.php';`
 2. Inicializas tus test según tus necesidades: cargar ficheros de configuración, declarar constantes...
 3. Declaras un array y lo vas rellenando de tests. Cada test es una instancia de la clase Zarangotest, y puedes especificar un título y una categoría (por si quieres agrupar tus tests para organizarlos mejor).
 4. Una vez declarado tu juego de tests, llamas a la función `zarangotest($juegoDeTests, "Un título")`.

Por ejemplo:

	<?php

	include 'zarangotest.php';

	$juegoDeTests = array();

	/** Test Mysqli */
	$juegoDeTests[] = new Zarangotest("Extensión mysqli", "Extensiones", function() {
	    return function_exists("mysqli_close");
	});

	zarangotest($juegoDeTests, "MiProyecto");

Como puedes ver, el contenido de los tests consiste en una función anónima que devuelve true o false. Cualquier otro valor devuelto se considerará como un fracaso en la ejecución del test y se mostrará en el informe. Así pues, ejecutamos el script y obtenemos el informe:

http://israelviana.es/wp-content/uploads/2013/08/zarangotest-2.gif

¿Y los tests _pre-fabricados_?
-------------------------------------

Zarangotest no está del todo terminado. Cuando lo esté, los tests _prefabricados_ estáran disponibles como clases separadas. Mientras tanto, puedes utilizar el código de [prefabricados.php](https://github.com/isra00/zarangotest/blob/master/prefabricados.php).

¿Y esto no se puede hacer con PHPUnit?
--------------------------------------

Sí, se puede. Aunque PHPUnit está pensado para tests unitarios, se puede utilizar para hacer tests de integración e incluso tests funcionales. Pero si no dominas PHPUnit o no quieres/puedes desplegarlo en tus servidores, Zarangotest es una buena alternativa. Además, Zarangotest genera directamente el informe en HTML, algo que no es tan ágil con PHPUnit.

Es todo por el momento. En las próximas semanas iré subiendo actualizaciones, mejoras y más documentación a [Zarangotest](https://github.com/isra00/zarangotest). Mientras tanto, sería genial que le echaras un vistazo y comentaras tus impresiones aquí :-)