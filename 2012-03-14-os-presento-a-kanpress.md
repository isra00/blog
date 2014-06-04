Os presento a Kanpress
======================

_14-03-2012_

¡Hola! Estas últimas semanas he estado desarrollando una idea que surgió de una necesidad real: coordinar el trabajo de los cerca de diez redactores que escriben en [Caminayven](http://www.caminayven.com) se trata de una revista católica que funciona con Wordpress. Si nuestro trabajo es escribir artículos y un artículo puede estar en estado _propuesto_, _en desarrollo_ o _pendiente de revisión_... ¿por qué no usar la metodología [Kanban](http://es.wikipedia.org/wiki/Kanban "Kanban en Wikipedia. Muy original, ¿verdad?")?

Así surgió la idea de [Kanpress](http://israelviana.es/kanpress_es "Homepage de Kanpress"), un [plug-in de Wordpress](http://wordpress.org/extend/plugins/kanpress/) que acerca los tableros Kanban al mundo editorial. Un artículo, una tarea. Proponer, asignar, desarrollar, revisar. Si quieres saber más sobre Kanpress, te remito a la [página del proyecto](http://www.israelviana.es/kanpress_es). Aquí contaré brevemente cómo está siendo la gestión de un proyecto de software libre non-profit, pequeño pero con vocación de crecer en usuarios y características.

El comienzo del proyecto fue sencillo: la idea, el prototipo sobre papel y una implementación rápida de las funcionalidades más básicas: crear tarea, asignarla, mover su estado, etc. En cuanto tuve una primera y muy verde versión beta, la subí a Caminayven para que los compañeros la probaran, detectando errores y recibiendo de ese feedback la priorización de las siguientes funcionalidades a implementar.

[![Boceto de Kanpress en papel](http://israelviana.es/wp-content/uploads/2013/02/454524114.jpg)](http://israelviana.es/wp-content/uploads/2013/02/454524114.jpg)

El siguiente hito fue la [Betabeers](http://betabeers.com/), reunión de programadores en Murcia, que aproveché para presentar Kanpress a un selecto grupo de espartanos que acogieron la idea con mucho interés. Para poder llegar al día de la presentación con algo decente trabajé en un sprint de dos días dedicados casi en exclusiva a mejorar la interfaz y pulir errores. Dos días en los que, por cierto, la técnica del [pomodoro](http://es.wikipedia.org/wiki/T%C3%A9cnica_Pomodoro) tuvo una importancia capital.

Es destacable el dato de que, cuando presenté Kanpress en la Betabeers (el pasado 8 de febrero), ni siquiera traía las rutinas de instalación necesarias para que el público pudiese probar Kanpress. Sólamente funcionaba en mi portátil y en caminayven.com. Pero funcionaba y tenía una interfaz decente, lo que me permitió causar buena impresión y captar algunos colaboradores. Pragmatismo en estado puro.

Cuando se celebró la Betabeers era demasiado pronto para que Kanpress rompiera el cascarón, pero las oportunidades hay que aprovecharlas. Los días siguientes fueron más de documentar y preparar Kanpress para su distribución que de agregar nuevas funcionalidades, así como de darle un poco de bombo ([algunos blogs](http://www.codigogeek.com/2012/02/12/kanpress-gestion-kanban-para-wordpress/) se hicieron eco de la existencia de Kanpress).

[![](http://israelviana.es/wp-content/uploads/2013/02/pagina-kanpress1-300x245.png "Página de Kanpress es WordPress.org")](http://wordpress.org/extend/plugins/kanpress/)El tercer hito fue enviar Kanpress al repositorio oficial de plug-ins de Wordpress, que fue aceptado al día siguiente. Esto suponía, principalmente, dos cosas: que Kanpress _tenía que hablar inglés_, y que debía usar el repositorio Subversion que me proporcionaba Wordpress (y esto era un problema porque desde el principio trabajé con GitHub).

Así que últimamente me he esforzado por internacionalizar Kanpress (inglés, español y gallego son los idiomas soportados actualmente) y he decidido dejar de usar GitHub, ya que no he conseguido de forma elegante sincronizar ambos repositorios. También he procurado hacer completa y atractiva la [página del plug-in en Wordpress](http://wordpress.org/extend/plugins/kanpress/).

En resumen:

*   Implementa la idea lo más rápido que puedas y pide opinión a gente (mejor si es gente que entienda).
*   Pónselo fácil a los usuarios: haz una buena página, bonita y atractiva.
*   Internacionaliza: seis mil millones de cabezas piensan más que 300 millones de hispanohablantes.
¿Algún comentario? ¿Gestionas algún proyecto de software libre? ¡Comparte tu opinión en los comentarios!
