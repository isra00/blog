Genera tus Entidad/Relación circulares con SchemaBall-PHP
=========================================================

_08-06-2009_

![Gráfico generado con SchemaBall-PHP](http://photos-b.ak.fbcdn.net/hphotos-ak-snc1/hs018.snc1/4521_1092417783185_1008733449_30223561_1804937_n.jpg "Gráfico generado con SchemaBall-PHP")

Inspirándome en el [SchemaBall de Martin Krzywinski](http://mkweb.bcgsc.ca/schemaball/) este fin de semana he escrito SchemaBall en PHP. Se trata de un generador de gráficos que representan las tablas de una base de datos y sus relaciones, al estilo de un diagrama entidad/relación. Aún está bastante verde, y la mayor carencia que tiene es que no distingue relaciones de cardinalidad N:N, es decir, aquellas donde la relación está expresada en una tabla intermedia y dos claves foráneas. Además, sólo es compatible con MySQL, aunque mi intención es darle compatibilidad con más gestores de bases de datos, e integrarlo en [MagiSQL](http://sourceforge.net/projects/magisql/), del que tendrás noticias en breve ;-)

Por lo demás, añadiré al gráfico alguna representación de los campos, para poder visualizar la complejidad de las tablas de algún modo. Además, me gustaría añadirle interactividad, pero para ello habría que cambiar completamente la plataforma gráfica, pasando de imágenes PNG generadas con [GD](http://www.phpgd.com/) al [Canvas de HTML](http://en.wikipedia.org/wiki/Canvas_(HTML_element)) o incluso Flash.

En fin, sólo es un adelanto para una librería que espero que crezca y se convierta en una herramienta útil para los diseñadores/administradores de bases de datos. ¿Opiniones? ¿Sugerencias? ¿Críticas? ¿Insultos racistas? Los comentarios están abiertos y en cuanto el SVN me funcione (hoy los astros se han alineado favorablemente xD) subiré el código.

PD: la librería para generar este tipo de gráficas estará disponible de forma independiente, para que se pueda utilizar para información diferente al esquema de una base de datos.