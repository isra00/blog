El ataque de eu2010.es en otra web de la administración
=======================================================

_12-01-2010_

Mucho revuelo se ha armado con el [ataque a la web de la presidencia europea de turno](http://www.securitybydefault.com/2010/01/eu2010es-el-fail-es-para.html). Llegó incluso a estar en la portada de El Mundo ([edición impresa](http://www.elmundo.es/papel/2010/01/05/Library/portada.pdf)).

Por su parte, desde la Secretaría de Estado de Comunicación reiteraron que la página no ha sido alterada por ningún intruso, ya que la imagen mostrada en el engaño ha sido cargada desde un servidor remoto y no es posible acceder a ella usando normalmente la web.

Y la verdad es que, aunque 11,9 millones por adaptar un OpenCMS y darle mantenimiento y "seguridad" es el robo del siglo, hay que admitir que el error era del propio OpenCMS (el gestor de contenidos que utilizan... que no es PHP :-P), y además un XSS, si no se utiliza para robar cookies o hijacking es prácticamente inofensivo.

No obstante, este [error de principiante](http://www.elmundo.es/elmundo/2010/01/04/union_europea/1262634659.html) no es el único pecado de Telefónica (o de la cárnica que haya subcontratado para el proyecto). [Santi Saez nos desvela](http://twitter.com/santisaez) que la web fue alojada en un servidor con un sistema operativo obsoleto (Debian 4), el panel de control Plesk accesible públicamente, el SSH accesible públicamente y permitiendo el login a root y un BIND (servidor de DNS) vulnerable. Además, el [servidor de nombres no era dedicado](http://filesocial.com/38f0w53). Ahora han intentado arreglarlo [externalizando el hosting en Akamai](http://twitter.com/santisaez/status/7515815619).

Pero no nos desviemos del tema, que es explicar cómo funciona esta vulnerabilidad, y **cómo explotarla en otra web de la administración** que todavía no ha parcheado el OpenCMS.

### Qué es XSS

Un ataque XSS o cross-site scripting es una vulnerabilidad de aplicaciones web que consiste básicamente en **conseguir que en la página víctima se cargue un script de otro sitio**. Como sabemos que los scripts (<del>casi</del> <ins>siempre</ins> JavaScript) se ejecutan en el navegador del cliente, es evidente que este ataque no va a alterar el servidor en nada.

Pero ¿cómo hacer que una página cargue un script? Para buscar vulnerabilidades XSS y cualquier otra vulnerabilidad web es esencial que encontrar **puntos de entrada** de datos por parte del usuario. Estas _puertas_ por las que introducimos datos suelen ser:

*   **URL (casi siempre parámetros)**: la dirección de una página la introducimos nosotros y la interpreta el servidor. Habitualmente suelen dar más juego los parámetros (en PHP, lo que se obtiene con $_GET[]). Es el que usaremos en nuestro caso práctico.
*   **Formularios**: en los formularios de login, búsqueda, etc se nos piden datos que después el servidor interpreta. Por aquí se suelen colar las inyecciones SQL, otro ataque que veremos en futuros posts y que puede llegar a ser muuuuy peligroso, a la vez que sencillo.
*   **Cabeceras**: son manejadas por los servidores. No suelen dar problemas, porque los desarrolladores web no suelen tocar ahí ;-)
*   **Cookies**: más complejas y difíciles de atacar, aunque pueden llegar a ser también muy peligrosas (autenticación falsa, etc). Es un tema interesante para un futuro post.
Pues bien, entremos en materia. Imagina un formulario de búsqueda de una web cualquiera. Hay un cuadro de texto en el que tú introduces una palabra, por ejemplo, 'normativas calidad' porque quieres buscar las normativas de calidad de un organismo público. Cuando pulsas "buscar", aparece una página de resultados en el que arriba dice "se han encontrado x resultados para 'normativas calidad'". Es decir, repite lo que le has dicho. Ha puesto en la página lo que tú habías introducido en el cajetín de búsqueda.

Vale, pero ¿y si en lugar de 'normativas calidad' pones '&lt;img src="http://bean.com/mister-bean.jpg"&gt;'? En teoría la página debe repetir lo que has escrito. Si la web no está protegida, entonces verás una linda foto de Míster Bean en la página de resultados, ya que el navegador interpreta el HTML que has introducido y el buscador ha repetido. Acabas de inyectar HTML, pero no has manipulado el servidor. Esa foto de Míster Bean la ves tú en tu navegador y nadie más.

Si en lugar de HTML plano introduces un script (bien por invocación bien por introducción directa del código), ese script se ejecutará en el navegador, y podrás manipular los elementos de la página, hacer redirecciones... lo que quieras, pero siempre sabiendo que eso se está ocurriendo en tu navegador y sólo en tu navegador.

En los formularios de búsqueda como ocurrió con eu2010.es esto no constituye un peligro real para la página (aunque si el sitio es famoso o asociado a política no te dará muy buena imagen), pero si esta inyección la introduces en un cuadro de texto para publicación (un foro, un comentario en un blog, etc), entonces las manipulaciones que hagas las verán todos los que accedan a esa página, y eso ya empieza a ser peligroso.

### Tutorial para ser hacker en 5 minutos

Vamos a ver este ataque en la práctica. La web del Centro de Investigaciones Sociológicas, construida sobre OpenCMS, tiene un lindo buscador. Si introducimos una palabra clave como "hola mundo", la página de resultados repetirá nuestras palabras como un loro:

![Página de resultados de búsqueda](http://photos-c.ak.fbcdn.net/hphotos-ak-snc3/hs162.snc3/18980_1223883949757_1008733449_30521913_3439634_n.jpg)

Aquí tenemos el código fuente:
<pre&lt;input class="txt" type="text" size="15" name="query" value=hola mundo &gt;</pre>
Primer fallo... no poner comillas para los valores, como exige el estándar HTML. Bueno, más fácil nos lo ponen.

Si en lugar de 'hola mundo' ponemos '&gt;hola mundo &lt;', entonces el código generado será
<pre>&lt;input class="txt" type="text" size="15" name="query" value=&gt;hola mundo &lt;&gt;</pre>
Lo que acabamos de hacer es inyectar código cerrando la tag input con el símbolo '&gt;', y el resultado será que vemos el texto box de búsqueda vacío y nuestro mensaje, 'hola mundo' a su lado. Bien, vamos a divertirnos. Si introducimos '&gt;&lt;script&gt;alert("Hola mundo")&lt;/script&gt;' inyectamos HTML con un JavaScript (la función alert() muestra un mensaje emergente), que se ejecuta con el siguiente resultado:

![XSS con un alert() en JavaScript](http://photos-e.ak.fbcdn.net/hphotos-ak-snc3/hs162.snc3/18980_1223883189738_1008733449_30521906_7262713_n.jpg)

Hala, ya somos super-hackers de la CIA voy corriendo a publicarlo en Meneame :-P. Ah, no, todavía nos falta el "golpe final", introducir la imagen de Mr. Bean. Ahora simplemente inyectamos una imagen (habiendo cerrado previamente la tag input con el signo '&gt;'):
<pre> &gt;&lt;img src="http://blog.tmcnet.com/blog/tom-keating/images/mr-bean.jpg"&gt; </pre>

![Mr. Bean en la web del CIS](http://photos-c.ak.fbcdn.net/hphotos-ak-snc3/hs162.snc3/18980_1223883229739_1008733449_30521907_7436113_n.jpg)

Si aparece repetida es porque en la página de resultados se muestra el mensaje de "buscar otra vez" en varios idiomas.

(Lo que viene ahora no es muy importante): hay una particularidad en este caso, y es que, nuestra inyección la podemos hacer tanto con la casilla de búsqueda (es decir, enviando el formulario por POST) como introduciendo el parámetro query el nombre del campo de texto en la propia URL (es decir, haciendo petición GET). Esto ocurre porque los [servlets](http://es.wikipedia.org/wiki/Java_Servlet)eso que se utiliza en Java para hacer aplicaciones web mete en el mismo "saco" (el método getParameter()) los parámetros introducidos por formularios y URL. Eso, combinado con la mala práctica de poner doPost en doGet y viceversa o sea, decirle a Java que haga lo mismo para las peticiones GET y POST hace que podamos inyectar código no sólo a través del campo de texto de búsqueda, sino también desde la URL. Así podremos distribuir nuestro super-hackeo a nuestros amigos, y al igual que pasó con eu2010.es, decir/tuitear...

##### ¡Mira, mira! ¡Mr. Bean también se fue al CIS! [http://is.gd/679lU](http://is.gd/679lU)

### ¿Cómo prevenir esta vulnerabilidad?

Si eres webmaster o desarrollador, ve y corre a comprobar si tus webs son vulnerables a este ataque. En caso de que hayas recibido una desagradable sorpresa, no tranquilizará saber que prevenirlo es tan sencillo como filtrar con una función la entrada del usuario. Imaginemos que tenemos un buscador en PHP que recibe las palabras clave por un formulario y un campo de texto llamado "busqueda". Si en nuestra página de resultados tenemos algo como
<pre lang="php">Se han encontrado <?php echo $numero_de_resultados ?&gt; resultados para &lt;?php echo $_POST['busqueda'] ?></pre>
sólo tendremos que sustituirlo por
<pre lang="php">Se han encontrado &lt;?php echo $numero_de_resultados ?&gt; resultados para &lt;?php echo htmlentities($_POST['busqueda']) ?&gt;</pre>
...y estaremos a salvo ([más información sobre htmlentities()](http://es2.php.net/function.htmlentities)). Aunque hay métodos más sofisticados y completos, este funciona muy bien, ya que sustituye los caracteres especiales de HTML en sus [entidades](http://www.utexas.edu/learn/html/spchar.html) correspondientes, de tal forma que el atacante no verá su código interpretado, sino mostrado tal y como lo escribió.

### Moraleja

¡No te fíes de los usuarios! Filtra todas las entradas (¡todas! formularios, URL, etc). Si esperas un valor numérico en un campo de texto, comprueba que es un número; si esperas una dirección de email, comprueba que es un email (pero con expresiones regulares ¿eh? nada de [cosas raras](http://www.israelviana.es/blog/Post/57/spaghetti-a-la-carbonara/)).

### Conclusión

Vivimos en la era del ladrillo, y en contra de lo que nuestros torpes políticos digan, el modelo de negocio del ladrillo no sólo no ha muerto, sino que se ha extendido como un cáncer hacia la industria informática. Nuestro negocio está lleno de gente con talento que cobra poco, y políticos corruptos que chupan generosas subvenciones y concursos públicos de una administración a la que no le importa malgastar un dinero que nos pertenece a todos.

Como dice un amigo... en España la ingeniería informática tiene tres principales salidas: por tierra, mar y aire.