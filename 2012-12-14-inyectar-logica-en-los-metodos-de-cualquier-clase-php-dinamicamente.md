Inyectar lógica en los métodos de cualquier clase PHP dinámicamente
===================================================================

_14-12-2012_

La [Programación Orientada a Aspectos](http://es.wikipedia.org/wiki/Programaci%C3%B3n_Orientada_a_Aspectos "RTFM!") (POA) no es la tendencia más de moda hoy en día, pero tiene bastantes implementaciones muy interesantes en diferentes lenguajes. Lo que a continuación presento es una implementación parcial y experimental de POA, aunque siendo puristas no sería POA realmente, ya que no se declaran los aspectos como tales. Además, solo hay dos <abbr title="puntos de ejecución donde un aspecto puede ser conectado, como una llamada a un método, el lanzamiento de una excepción o la modificación de un campo">join points</abbr>: preMethod y postMethod (antes y después de un método).

Se trata de inyectar funciones antes y/o después de cualquier método de cualquier clase que queramos. Estas funciones podrán manipular la forma en que se ejecutan los métodos objetivo, modificando los parámetros de llamada o incluso deteniendo su ejecución, así como manipular su salida.

Para ver más claro de qué va todo esto, imaginemos una clase sencilla:

[php]class Coche
{
private $posicion = 0;

public function avanzar($incremento)
{
$this-&gt;posicion += $incremento;
}

public function getPosicion()
{
return $this-&gt;posicion;
}
}[/php]

El método avanzar() aumentará la propiedad $posicion y no hará nada más. No implementa restricciones de máximo, mínimo, etc. En principio, el método está ya escrito y no se puede modificar en tiempo de ejecución.

Pongamos por caso que queremos introducir ciertas restricciones en avanzar(), por ejemplo, que el atributo $posicion nunca sea superior a 10. Sería genial poder inyectar una función que, antes de ejecutar avanzar(), que hiciese la comprobación y cancelase la ejecución del método en caso de que la $posicion resultante fuese mayor a 10.

Pues esto es exactamente lo que vamos a hacer con... ¡tachán! [**ClassTriggers**](https://github.com/isra00/class-triggers):

[php]//Instanciamos un ClassTriggers que envuelve nuestro objeto Coche
$cocheInterceptado = new ClassTriggers(new Coche);

//Definimos una acción que se ejecutará antes (preMethod) del método avanzar()
$cocheInterceptado-&gt;bind('avanzar', 'preMethod', function(&amp;$arguments) {
echo 'Has solicitado avanzar(' . $arguments[0] . &quot;)
&quot;;

if ($this-&gt;posicion + $arguments[0] &gt; 10) {
echo 'No puedes avanzar más de 10. Deteniendo ejecución del método';
return ClassTriggers::COND_STOP_EXECUTION;
}
});

$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;

$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;

$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;

$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;[/php]

Como puedes ver, tenemos una función anónima que recibe el parámetro $arguments. Este array contendrá los parámetros de la llamada a avanzar(), así podemos interceptar el valor que se quiere incrementar a $posicion. Además, como el array se pasa [por referencia](http://php.net/manual/es/functions.arguments.php#functions.arguments.by-reference "Paso de parámetros por referencia en PHP") tenemos la posibilidad de manipular los parámetros de llamada.

En este caso no lo hacemos, sino que devolvemos el valor especial <kbd>ClassTriggers::COND_STOP_EXECUTION</kbd>, que sirve para cancelar la ejecución del método. Es decir, si la suma de $posicion + $incremento es superior a 10, el método avanzar() no se ejecutará realmente.

La salida que producirá el script será la siguiente:
<pre>Has solicitado avanzar(3)
3
Has solicitado avanzar(3)
6
Has solicitado avanzar(3)
9
Has solicitado avanzar(3)
No puedes avanzar más de 10. Deteniendo ejecución del método
9</pre>
Es decir, tratamos de sumar 3 cuatro veces, pero después de la tercera llamada (cuando $posicion vale 9), el método que hemos inyectado detecta que quieres incrementar $posicion por encima de 10, y cancela la ejecución del método.

Llegados a este punto, el abanico de posibilidades es muy amplio, pero espera, que aún queda más ;-)

Es posible definir más de una acción para un evento (los eventos posibles son preMethod y postMethod) y método, y se irán ejecutando en el mismo orden en que se definieron:

[php]$cocheInterceptado-&gt;bind('avanzar', 'preMethod', function(&amp;$arguments) {
echo &quot;Una acción antes de avanzar()
&quot;;
});
$cocheInterceptado-&gt;bind('avanzar', 'preMethod', function(&amp;$arguments) {
echo &quot;Otra acción
&quot;;
});[/php]

Por otra parte, el trigger postMethod se ejecuta después del método real, y las acciones podrán acceder al valor devuelto por el método original, para poder evaluarlo, manipularlo, etc. Siguiendo el ejemplo del coche que no debe avanzar más de 10 posiciones, podemos implementar la restricción después de ejecutar avanzar():

[php]$cocheInterceptado-&gt;bind('avanzar', 'postMethod', function(&amp;$arguments, $output) {
if ($this-&gt;posicion &gt; 10)
{
$this-&gt;posicion = 10;
}
});

$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;
$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;
$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;
$cocheInterceptado-&gt;avanzar(3);
echo $cocheInterceptado-&gt;getPosicion() . &quot;
&quot;;[/php]

En este ejemplo se llama cuatro veces a avanzar(3), por lo que, después de la primera llamada, $posición valdrá 3, después de la segunda 6, después de la tercera 9, y después de la cuarta 12... en teoría:
<pre>3
6
9
10</pre>
Como puedes ver, el valor final no es 12 sino 10, debido a la acción que hemos ejecutado.

### Valores especiales

Ya hemos visto por encima para qué sirve <kbd>ClassTriggers::COND_STOP_EXECUTION</kbd>. Devolviendo este valor en una acción, ClassTriggers hará que no se ejecute realmente el método solicitado.

Otro valor especial que se puede devolver es <kbd>ClassTriggers::NO_MORE_ACTIONS</kbd>. Con esto indicaremos que, después de ejecutar la acción que devuelve este valor, no se ejecutará ninguna acción más para ese trigger y ese método. Por ejemplo:

[php]$cocheInterceptado-&gt;bind('avanzar', 'postMethod', function(&amp;$arguments) {
if ($this-&gt;posicion &gt; 10)
{
$this-&gt;posicion = 10;
}
return ClassTriggers::NO_MORE_ACTIONS;
});

$cocheInterceptado-&gt;bind('avanzar', 'postMethod', function(&amp;$arguments) {
$this-&gt;posicion = 99;
});

$cocheInterceptado-&gt;avanzar(11);
[/php]

Si la primera acción no devolviese <kbd>ClassTriggers::NO_MORE_ACTIONS</kbd>, la segunda acción se ejecutaría siempre, y en todos los casos $cocheInterceptado-&gt;posicion valdría 99. Pero devolviendo <kbd>ClassTriggers::NO_MORE_ACTIONS</kbd> se detiene la ejecución de más acciones.

### ¡Ojo!

Es importante resaltar que **las acciones inyectadas operan a nivel de objeto**, y no de clase, por lo que si tenemos dos instancias diferentes de Coche, inyectar lógica en una de ellos no modificará en absoluto a la otra. La posibilidad de reutilizar las acciones de un objeto para otras instancias, agrupándolas en **aspectos** (según la terminología de POA), no ha sido implementada, pero no sería complicado.

Otro dato importante: ClassTriggers **sólo funciona con PHP 5.4**, ya que hace uso de <kbd>[Closure::bind()](http://php.net/manual/es/closure.bind.php)</kbd> para que las acciones puedan acceder a $this.

### Posibles ampliaciones

Algunas ideas factibles siguiendo el modelo de ClassTriggers:

*   Añadir un trigger (join point en la terminología de AOP) que se ejecute cuando el método objetivo lance una excepción.
*   Añadir un trigger que se ejecute cuando un atributo del objeto se modifique (se puede implementar con <kbd>[__set](http://php.net/manual/es/language.oop5.overloading.php#object.set)</kbd>).
*   Añadir un trigger que se ejecute cuando un atributo del objeto se lea (se puede implementar con <kbd>[__get](http://php.net/manual/es/language.oop5.overloading.php#object.get)</kbd>).
*   **Join point introspection**: Que una acción sepa si para ese trigger hay otras acciones, cuáles son e incluso poder manipular su ejecución. Para ello las acciones deberían poder nombrarse.

### Conclusiones

Ha sido divertido pensar e implementar [ClassTriggers](https://github.com/isra00/class-triggers). Si echas un vistazo al [código](https://github.com/isra00/class-triggers/blob/master/src/ClassTriggers/ClassTriggers.php) verás que es bastante sencillo y pequeño (¡140 líneas!), aunque hace uso de closures y métodos mágicos (no es apto para principiantes).

Algunas aplicaciones prácticas pueden ser:

*   Inserción de pre-condiciones y post-condiciones en el acceso a bases de datos (auto-iniciar/terminar transacciones, manipular las sentencias SQL...).
*   **Auto-guardado de objetos en base de datos**: en un objeto con getters y setters de toda la vida, auto-guardar en la base de datos los valores que cambien. Es decir, que cuando llames a <kbd>$cliente-&gt;setNombre('Pepe')</kbd> automáticamente se guarde ese valor en la base de datos.
*   Hacer un sistema diabólico de **versionado de objetos** (sí, sí, como Subversion, pero con [POPOs](http://www.javaleaks.org/open-source/php/plain-old-php-object.html)).
*   Validar los parámetros de los métodos sin tener que duplicar un montón de código.
No obstante, en la mayoría de aplicaciones del mundo real es más apropiado usar simplemente la herencia de clases y sobreescritura de métodos para introducir pre/post-condiciones a un método cualquiera. Depende del diseño general, de qué nivel de modularidad que necesites y de las ganas que tengas de usar un montón de closures... porque al fin y al cabo, esto no es JavaScript :-P

¡Espero tus comentarios!