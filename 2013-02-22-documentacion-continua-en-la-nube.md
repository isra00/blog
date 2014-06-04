Documentación continua en la nube
=================================

_22-02-2013_

Tenemos [Travis](http://travis-ci.org) para integración continua, [Scrutinizer](https://scrutinizer-ci.com/) para inspección continua... pero hasta hace unos días no existía un servicio para generar **documentación continua** de proyectos.

Para mí es un follón generar y subir a la web los phpDocs de mis proyectos de software libre cada vez que hago commit (ya que quiero tenerlos siempre actualizados); y también es un follón administrar y configurar Jenkins u otros servicios de integración continua para que generen los docs con cada build.

Así que he pensado que no sería tan complicado hacer un sistema que genere y aloje los documentos de mis proyectos en GitHub. Y si es útil para mí, también puede serlo para otros.

Dicho y hecho. Aquí lo tienes, tan sencillo como autorizar la aplicación en tu cuenta en GitHub y elegir qué repositorios quieres documentar. Cada vez que hagas push al repositorio, los documentos serán automáticamente generados y podrás verlos en `github-phpdoc.israelviana.es/docs/[tu nombre de usuario GitHub]/[nombre del repositorio]`:

[![Github-phpDocs landing page](http://israelviana.es/wp-content/uploads/2013/02/github-phpdoc1.png)](http://github-phpdoc.israelviana.es/?utm_source=Blog%2Bisraelviana.es&amp;utm_medium=Post%2Bpresentaci%C3%B3n&amp;utm_term=Imagen%2Blanding&amp;utm_campaign=Enlaces%2Bpromocionados)

[![Github-phpDocs landing repo list](http://israelviana.es/wp-content/uploads/2013/02/github-phpdoc2.png)](http://github-phpdoc.israelviana.es/?utm_source=Blog%2Bisraelviana.es&amp;utm_medium=Post%2Bpresentaci%C3%B3n&amp;utm_term=Imagen%2Brepo_list&amp;utm_campaign=Enlaces%2Bpromocionados)

[![GitHub-phpDoc documentación](http://israelviana.es/wp-content/uploads/2013/02/github-phpdoc3.png)](http://github-phpdoc.israelviana.es/?utm_source=Blog%2Bisraelviana.es&amp;utm_medium=Post%2Bpresentaci%C3%B3n&amp;utm_term=Imagen%2Bdocs&amp;utm_campaign=Enlaces%2Bpromocionados)

##### Puedes probarlo ya en [github-phpdoc.israelviana.es](http://github-phpdoc.israelviana.es/?utm_source=Blog%2Bisraelviana.es&amp;utm_medium=Post%2Bpresentaci%C3%B3n&amp;utm_term=link%2Bc%C3%B3digo&amp;utm_campaign=Enlaces%2Bpromocionados)

El servicio es gratuito y open-source; por supuesto, el [código está en GitHub](https://github.com/isra00/online-phpdoc) y la [documentación en la nube](http://github-phpdoc.israelviana.es/docs/isra00/online-phpdoc/) ;-)

De momento solo soporta proyectos en PHP alojados en GitHub (repositorios públicos de momento), pero si hay suficiente demanda se puede ampliar a otros lenguajes o alojamientos.

¿Y tú, generas documentación del código de tus proyectos? Si tienes proyectos PHP en GitHub te agradecería que probases el servicio y me dieras todo el feedback posible. ¡Gracias de antemano!

Y por último, una pregunta: ¿Estarías dispuesto a pagar 2 por documentación privada de repositorios privados?