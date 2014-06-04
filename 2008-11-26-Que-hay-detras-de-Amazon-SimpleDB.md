¿Qué hay detrás de Amazon SimpleDB?
===================================

_26-11-2008_

Estos días he estado informándome sobre los Amazon Web Services, más concretamente el relativamente reciente **[SimpleDB](http://aws.amazon.com/simpledb/)**, un poco por escepticismo hacia la moda del Cloud Computing, o mejor dicho, a la moda de _hablar sobre_ el Cloud Computing (qué queréis que os diga, tampoco me parece tan novedoso el invento). El servicio es un gestor de BBDD no relacional y no orientado a objetos, situado en _nosedonde_, gestionado de _nosequé manera_ (cloud computing, vamos). Este gestor de BBDD es tan sencillo de entender como que tenemos dominios y, dentro de los dominios, registros con pares propiedad-valor. Estos pares no están definidos, por lo que podemos decir que es una base de datos sin esquema, sin estructura previamente definida. Genial, parece útil para ciertos programas.

El caso es que desde hace algún tiempo estoy recopilando información sobre Oracle BerkeleyDB (Oracle compró SleepyCat, desarrolladora original), un gestor de bases de datos embebido, no relacional, no orientado a objetos, y encima Open Source. Genial, parece útil para ciertos programas. Coño, dejá vu. Y encima voy a la web de BerkeleyDB y me encuentro este [case study](http://www.oracle.com/customers/snapshots/amazon-oracle-berkeley-db-casestudy.pdf) de Julio de 2008 en el que Tim Kohn, jefe de servicios explica que BerkeleyDB es usado como caché para la gran base de datos de Amazon (como tienda, no como proveedor de servicios web), delante de un Oracle DB _normal_ (el artículo indica el talento que tienen en Amazon).

Interesante. Buscando un poco más me encuentro con que el desarrollo de Amazon para integrar BerkeleyDB, Oracle y los demás sistemas ha evolucionado y le han llamado [Dynamo](http://www.allthingsdistributed.com/2007/10/amazons_dynamo.html). Aunque su autor empieza diciendo que:

> Dynamo is not directly exposed externally as a web service; however, Dynamo and similar Amazon technologies are used to power parts of our Amazon Web Services, such as S3.

...aunque teniendo en cuenta que fue publicado hace un año, la verdad es que es bastante probable que SimpleDB sea una interfaz, o directamente instancias, de Dynamo, esa combinación de BerkeleyDB y MySQL:

> [...] BDB can handle objects typically in the order of tens of kilobytes whereas MySQL can handle objects of larger sizes. Applications choose Dynamo?s local persistence engine based on their object size distribution. The majority of Dynamo?s production instances use BDB Transactional Data Store.

**Conclusiones**:

*   Leyendo ambos artículos uno se da cuenta de lo buenos que son los informáticos de Amazon. Normal, dado que son una de las empresas más potentes de Internet y manejan una de las Base de Datos comerciales más grandes del mundo.
*   Open Source matters, como dicen los de Joomla. Y las empresas lo saben, cada vez mejor. Y sobre todo por las malas lenguas que desprecian a MySQL como RDBMS incompleto en comparación con PostgreSQL. Soy fanático de MySQL y es cierto que es un DBMS muy sencillo, está algo verde y en algunas cosas resulta "de juguete" (sobre todo en comparación con el gigante Oracle), pero la escalabilidad y rendimiento que ofrecen lo hacen líder para Internet y entornos como los de AmazonWS.
*   En el caso de BerkeleyDB ha habido una apuesta fuerte por parte de Oracle, aunque Amazon [ya la usaba](http://findarticles.com/p/articles/mi_m0EIN/is_/ai_74803333) antes de ser adquirida por el grande de Redwood City. Un DBMS original, sencillo, que desafía a los tradicionales RDBMS que nos enseñan en la universidad como la única solución posible. Además, existe BerkeleyDB XML, una interesante Base de Datos estructurada que emplea XQuery. Puede dar mucho que hablar en el futuro, sobre todo de cara a las BBDD semánticas.
*   El cloud computing en BBDD, como las demás tendencias _2.0_ (odio esa etiqueta, la odio, la odio!!) no aporta ninguna innovación tecnológica (en este caso Dynamo ha sido un implementación para hacer posible una aplicación, pero BDB y MySQL ya existían), pero es una gran oportunidad para ahorrar costes en mantenimiento y tiempo en desarrollo.

**¿Qué opinión os merece SimpleDB? ¿Y los servicios de cloud computing en general? ¿Os gustan, no os gustan, ponen en peligro vuestros empleos...?**