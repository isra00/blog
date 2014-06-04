Arquitectura para una API web en 5 minutos
==========================================

_27-03-2009_

Transcribo parte de un hilo en mi [foro favorito](http://www.forosdelweb.com/f18):

> Hola, tengo un sitio en el cual queiro implementar una API para permitir a los sitios que tengan su clave insertar registros en mi base de datos controladamente... algun dato para arrancar?

Tienes que pensar que para cualquier API hay peticiones y respuestas. Las peticiones tienen argumentos y las respuestas, contenido, independientemente de lo que se haga en el servidor, lectura, escritura o baile ;-) Es decir, a **nivel de API no hay peticiones de lectura o escritura**, eso dependen de la funcionalidad que se implemente. Todas las peticiones HTTP son "iguales" (con _muchas_ comillas).

Ahora bien, para esas cosas hay muchas opciones. Si quieres algo sencillo a la vez que bastante compatible entre plataformas te recomiendo HTTP-GET para las peticiones y HTTP-JSON para las respuestas. Por ejemplo:

<kbd>http://servidor.com/api/editarCliente.php?clave=xxxx&amp;id=23&amp;nombre=Pepito</kbd>

Esto sería una petición que, en principio no tendría respuesta con contenido. Es decir, una vez enviada esa petición, se procesa y no hay nada que decir al cliente. O sí... un mensaje de "ok" o "error", aunque sólo sea eso..

Para una petición típicamente de lectura podríamos hacer algo como

<kbd>http://servidor.com/api/clientesNuevos.php?clave=xxxx&amp;limite=20</kbd>

Esta petición requeriría los 20 últimos clientes añadidos. El PHP puede ejecutar la consulta, recorrer los resultados y guardarlos en un array. En cuanto tengas el array de resultados, lo único que hay que hacer es:

<pre>
//Disponible a partir de PHP 5.2

echo $json_encode($my_array);
</pre>

JSON es un formato de representación de datos compatible con JavaScript y muy usado últimamente. Con estas herramientas podrás implementar APIs de manera rápida y sencilla, a la vez que compatibles con una amplia gama de lenguajes y plataformas. Puedes usarlo también desde tu propia aplicación si deseas una interfaz AJAX, puesto que en JavaScript no necesitas descodificar JSON, ya que es un formato nativo de JavScript, como ya he comentado.