5 razones para no usar Smarty (o similares)
===========================================

_21-11-2009_

[![Ejemplo de uso de Smarty](http://israelviana.es/wp-content/uploads/2009/11/smarty-ejemplo.gif)](http://israelviana.es/wp-content/uploads/2009/11/smarty-ejemplo.gif)En la mayoría de los casos, Smarty no es la solución idónea para separar la lógica de las vistas, aunque hay que admitir que la versión 3 está siendo un salto de calidad impresionante. No obstante, estas son las cinco principales razones que he encontrado para no incluirlo más en mis proyectos:

1.  **PHP** es un lenguaje con sintaxis embebida, es decir, **es de por sí un sistema de plantillas**. Para mayor claridad puedes utilizar la tag de apertura corta (<kbd>&lt;?</kbd>en lugar de<kbd>&lt;?php</kbd>) y el operador de impresión (<kbd>&lt;?=$nombre ?&gt;</kbd> en lugar de <kbd>&lt;?php echo $nombre ?&gt;</kbd>). Además, Smarty no añade funcionalidad.
2.  Con Smarty debes aprender una **nueva sintaxis** para imprimir variables, hacer bucles, etc.
3.  En aplicaciones complejas tendrás que crear un montón de **plug-ins** para cargar widgets u otros fragmentos de código para las vistas que quieras reutilizar. O bien utilizar cada dos por tres la tag {php}.
4.  Smarty es **difícil de integrar** en gestores de contenido, frameworks y editores de código.
5.  Smarty **reduce el rendimiento** de tus aplicaciones web ya que debe parsear y convertir a PHP las plantillas. Incluso con caché el rendimiento se ve afectado (más accesos a disco).
Ya habíamos hablado de este tema en [Plantillas PHP: cuestión de rendimiento](http://www.israelviana.es/blog/Post/40/plantillas-php-cuestion-de-rendimiento/), proponiendo el funcionamiento básico de un motor de plantillas basado en PHP puro.

#### ACTUALIZACIÓN (<abbr title="2009-10-23T18:27">23 de noviembre de 2009</abbr>):

Algunos expertos han escrito artículos en profundidad sobre este tema, por ejemplo ["Template Engines" de Brian Lozer](http://www.massassi.com/php/articles/template_engines/).

Con respecto a las alternativas, [Savant](http://phpsavant.com/) y [PHPTemplate](http://drupal.org/project/phptemplate) (creado para Drupal) son buenas opciones. Además, los principales frameworks (CakePHP, etc) y gestores de contenido (Joomla!, Wordpress) suelen incluir sus propios motores de plantillas en PHP.
> «In short, the point of template engines should be to separate your business logic from your presentation logic, not separate your PHP code from your HTML code.»

#### ACTUALIZACIÓN (<abbr title="2013-01-31T21:22">31 de enero de 2013</abbr>):

En este artículo no defiendo que los sistemas de plantillas sean malos siempre. No son ni malos por naturaleza, y hay muchos tipos de proyectos en los que tiene sentido usarlos. Personalmente no los he necesitado nunca, aunque si tengo que elegir uno elijo [Twig](http://twig.sensiolabs.org/), sin duda.