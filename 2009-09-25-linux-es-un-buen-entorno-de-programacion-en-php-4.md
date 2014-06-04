Linux es un buen entorno de programación en PHP
===============================================

_25-09-2009_

Siempre he apostado por el software libre, pero hasta hace no mucho usaba Windows, un sistema privativo, para desarrollar. No me convencían las herramientas de programación en PHP para Linux, ya que estaba muy contento con [Notepad++](http://notepad-plus.sourceforge.net/es/site.htm) para escribir scripts y [TortoiseSVN](http://tortoisesvn.tigris.org/) para trabajar con Subversion. Usaba un servidor [WAMP](http://www.wampserver.com) y [phpMyAdmin](http://www.phpmyadmin.net/) para administrar la base de datos.

Ahora, sobre Debian, trabajo con unas herramientas que no tienen nada que envidiar a las anteriores. Vayamos por partes.

### Un editor de PHP para Linux

Si algo me faltaba en Notepad++ era un autocompletado de código potente (el de Notepad++ se basa solamente en las palabras del archivo actual) y la autodocumentación en línea (es decir, soporte de [phpDoc](http://phpdoc.org/)). Tras darle una oportunidad a [Eclipse/PDT](http://www.eclipse.org/pdt/), me he decidido definitivamente por **[NetBeans](http://www.netbeans.org/features/php/index.html)**. Tiene integración con Subversion, phpDoc, [XDebug](http://www.xdebug.org/) y Firefox, y me parece más productivo que Eclipse. Una de las características que más me gusta es que tiene en cuenta la cláusula @return de los comentarios phpDoc de una función para entender el tipo de dato que devuelve:

[![NetBeans, un buen IDE para programar con PHP](http://israelviana.es/wp-content/uploads/2009/09/NetBeans.png "NetBeans, un buen IDE para programar con PHP")](http://israelviana.es/wp-content/uploads/2009/09/NetBeans.png)

No obstante, no he dejado de usar Notepad++, gracias a [WINE](http://www.winehq.org/). Lo utilizo para manejar archivos de diferentes codificaciones, procesar textos con expresiones regulares y escribir PHP cuando no me apetece iniciar NetBeans ;-)

### Un cliente Subversion para Linux

Como ya he dicho, NetBeans tiene soporte nativo de Subversion. En otros casos, utilizo [kdesvn](http://kdesvn.alwins-world.de/) y [RapidSVN](http://rapidsvn.tigris.org/). Además, he aprendido el valioso placer del cliente [svn por línea de comandos](http://svnbook.red-bean.com/en/1.4/svn.ref.svn.html) ;-) Por cierto, existe un cliente privativo aunque muy potente llamado [SyncroSVN](http://www.syncrosvnclient.com/), disponible para Windows, Mac y Linux.

### Un depurador de PHP para Linux

De nuevo NetBeans me soluciona la papeleta, pero también utilizo [KCacheGrind](http://kcachegrind.sourceforge.net/html/Home.html), un excelente visor de volcados XDebug:

[![KCacheGrind, herramienta para visualizar volcados de XDebug](http://israelviana.es/wp-content/uploads/2009/09/KCacheGrind.png "KCacheGrind, herramienta para visualizar volcados de XDebug")](http://israelviana.es/wp-content/uploads/2009/09/KCacheGrind.png)

### Cross-browser en Linux

Si algo abunda en GNU/Linux son los navegadores. Los de siempre, Firefox (con el maravilloso Firebug), Opera, Chrome y [lynx](http://es.wikipedia.org/wiki/Lynx_%28navegador%29) (o links o elinks), además de Konqueror (que nadie usa). Para el malvado Internet Explorer utilizo una máquina virtual con Windows XP en la que dispongo de muchas versiones de IE, gracias a [Internet Explorer Collection](http://utilu.com/IECollection/). Además, existen [renderizadores de Internet Explorer on-line](http://ipinfo.info/netrenderer/).

[![Internet Explorer](http://israelviana.es/wp-content/uploads/2009/09/ie1-300x240.png "Internet Explorer")](http://israelviana.es/wp-content/uploads/2009/09/ie1.png)

### Instalar extensiones es más fácil en Linux

Los paquetes Apache+PHP para Windows suelen traer algunas extensiones, pero a la hora de instalar otras nuevas (tanto [PECL](http://pecl.php.net/) como [PEAR](http://pear.php.net)) los métodos varían, y las utilidades de línea de comandos no siempre funcionan.

En Linux muchas extensiones PECL y bibliotecas PEAR están disponibles a través de los repositorios.

[![Repositorio Debian con paquetes PHP](http://israelviana.es/wp-content/uploads/2009/09/phpRepo.png "Repositorio Debian con paquetes PHP")](http://israelviana.es/wp-content/uploads/2009/09/phpRepo.png)

Además, el paquete de desarrollo php5-dev permite instalar compilando las extensiones de la forma más sencilla:

<kbd># pecl install nombre_de_la_extension</kbd>

Y lo mismo para librerías PEAR:

<kbd># pear install nombre_de_la_biblioteca</kbd>

### Conclusión

Si aún trabajas con Windows, anímate a probar GNU/Linux con Ubuntu o la distribución que más te guste. En una hora habrás podido instalar todas las aplicaciones que necesitas para programar de forma productiva y cómoda con PHP. Si ya lo has hecho, ¿qué experiencia tienes? ¿utilizas alguna de las herramientas que he citado? Comentar es gratis ;-)

**ACTUALIZACIÓN 14/dic/2012**: llevo unos meses usando PhpStorm en el trabajo y estoy encantado. Es mucho más rápido y potente que NetBeans, y las opciones de personalización son casi infinitas. Lo malo... que es privativo y [caro](http://www.jetbrains.com/phpstorm/buy/index.jsp).

**ACTUALIZACIÓN 14/feb/2013**: he dejado de usar Subversion para mis proyectos personales. Me gusta más [**git**](http://www.git-scm.com/), por su velocidad y su flexibilidad. Utilizo [GitHub](https://github.com/isra00 "Mi perfil en GitHub") para los repositorios públicos y [BitBucket](http://bitbucket.org) para los privados (ambos gratuitos), casi siempre desde la consola. Si quieres aprender Git, los cracks de GitHub han hecho [un tutorial interactivo impresionante](http://try.github.com/).