PHPTAL, un digno competidor de Smarty
=====================================

_21-03-2009_

Hoy he descubierto un sistema de plantillas para PHP que no conocía: [PHPTAL](http://phptal.org/). Está basado en [TAL](http://en.wikipedia.org/wiki/Template_Attribute_Language) (Template Attribute Language), la sintaxis que utiliza ZPT, el sistema de plantillas de [Zope](http://www.zope.org), del que ya hemos hablado (si tuviese tiempo para aprender Python, me metería a saco con este maravilloso framework).

Este sistema de plantillas, a diferencia de Smarty (que vengo usando hasta ahora) está orientado a XML-XHTML. Qué mejor que un ejemplo:

#### Con TAL

<pre class="xml">&lt;tal:block metal:define-macro="bloc"&gt;
&lt;div class="pagination" tal:condition="php: isset(pagination)"&gt;
&lt;tal:block repeat="item pagination"&gt;
&lt;tal:block condition="php: !empty(item.link)"&gt;

&lt;a tal:attributes="href item/link | ''" tal:content="item/label"/&gt;
&lt;/tal:block&gt;
&lt;tal:block condition="php: empty(item.link)" content="item/label"/&gt;

&lt;/tal:block&gt;
&lt;/div&gt;
&lt;/tal:block&gt;</pre>

#### Con Smarty

<pre>&lt;div class="pagination"&gt;
{foreach from=$pagination item=page}
{if $page.lien != ""}
&lt;a href="{$page.lien}"&gt;{$page.label}&lt;/a&gt;

{else}
{$page.label}
{/if}
{/foreach}
&lt;/div&gt;</pre>
A simple vista, se ve la diferencia sintáctica que ya he mencionado: la orientación a XML de TAL no permite generar CSS, por ejemplo, como sí permite Smarty. No obstante, esta limitación puede ser una gran ventaja, ya que con TAL es más fácil generar XHTML válido y trabajar con herramientas de autor (Dreamweaver, etc). Además, ofrece ciertas características de seguridad como validación de contenido inapropiado en los valores (inyección de JavaScript, etc). Y seguramente la sintaxis TAL permita transportar plantillas entre Zope y PHP con facilidad (aún no lo he comprobado in situ). También hay varios motores TAL para Perl, Java y Python (sin Zope), según la [documentación de ZPT](http://wiki.zope.org/ZPT/FrontPage).

Otra de las ventajas (bueno, subjetivamente) de TAL es su orientación a objetos, bastante limitada en Smarty (por ejemplo, [no soporta clases estáticas](http://www.phpinsider.com/smarty-forum/viewtopic.php?p=36201)).

Los defensores de PHPTAL [destacan la calidad del compilador](http://lists.motion-twin.com/pipermail/phptal/2005-July/000426.html) en comparación con Smarty, que dicen poco adecuado a las últimas versiones de PHP.

Desde luego PHPTAL se presenta como una alternativa realmente digna, y bastante adecuada a la metodología orientada a objetos que estoy siguiendo en el [patrón multivista](http://israelviana.es/patrones-de-desarrollo-web-multivista-4/) (prometo publicar la segunda parte en breve). Como contrapartida, el no poder trabajar con documentos no XML. Zope lo soluciona incluyendo otro motor de plantillas llamado DTML, que pese a su sugerente nombre no es orientado a XML/HTML. Aunque personalmente no me parece una solución óptima para muchos proyectos meter un motor de plantillas más para, seguramente, CSS y JavaScript sólamente.