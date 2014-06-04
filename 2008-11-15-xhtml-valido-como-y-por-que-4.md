XHTML válido: cómo y por qué
============================

_15-11-2008_

En [HTML Blog](http://htmlblog.net) encuentro [9 interesantes consejos](http://htmlblog.net/9-tips-to-validate-your-xhtml-code/) para escribir c&oacute;digo XHTML v&aacute;lido. Siempre le sugiero a la gente que escriba (y digo escribir, no generar :-P) XHTML v&aacute;lido (puede haber un interesante debate entre XHTML o HTML, sobre todo teniendo en cuenta las promesas de HTML 5), ya que al ser est&aacute;ndar respetamos las convenciones que los que saben m&aacute;s que nosotros han aceptado, al margen de darnos una cierta seguridad sobre la portabilidad de nuestras p&aacute;ginas.

Habitualmente, la gente que no se preocupa de validar su c&oacute;digo, suele acercarse al HTML (sintaxis SGML) que al XHTML (sintaxis XML). Algunas diferencias son:

*   Nombres de las etiquetas en min&uacute;sculas. En HTMLlas may&uacute;sculas eran un est&aacute;ndar de facto, en XHTML las min&uacute;sculas son obligatorias.
*   Poner barra en las etiquetas sin cierre: <pre>&lt;img src=&quot;imagen.png&quot; alt=&quot;Imagen&quot; /&gt;</pre> (por cierto, alt obligatorio en todas las im&aacute;genes).
*   Caracteres especiales como entidades XML: &quot;&amp;quot;&quot; en vez de &quot;&gt;&quot;.
*   C&oacute;digo JavaScript o CSS como contenido XML codificado (esto no lo hace casi nadie):<pre lang="html">&lt;script type=&quot;text/javascript&quot;&gt;
/* &lt;![CDATA[ */
var myfunction = function(){
&nbsp;
};
/* ]]&gt; */
&lt;/script&gt;</pre>

Cada vez m&aacute;s gente se va adaptando a los est&aacute;ndares, sobre todo XHTML, ya que a la larga podr&aacute; adaptarse mejor a los est&aacute;ndares de la web sem&aacute;ntica, tales como
[RDFa](http://www.w3.org/TR/xhtml-rdfa-primer/).