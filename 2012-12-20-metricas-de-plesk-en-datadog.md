Métricas de Plesk en Datadog
============================

_20-12-2012_

Hace algunos meses que uso [Datadog](http://datadoghq.com), una aplicación de monitorización en la nube. Su uso es sencillo: instalas el daemon en tu servidor, y ¡a disfrutar! Gráficas en tiempo real, alertas... ¡mola mucho!

Al configurarlo con una máquina en la que alojo varias webs con Plesk, pensé que sería interesante contar con métricas de [Plesk](http://www.parallels.com/es/products/plesk/), el panel de control con el que administro el servidor. Y aunque Datadog no da soporte a Plesk, tienen una preciosa API que he utilizado para sacar algunos datos:

*   Tráfico mensual por cada dominio
*   Número de dominios
*   Número de clientes
*   Número de cuentas de e-mail
*   Espacio de disco duro ocupado
*   Visitas mensuales por dominio (los que tengan activado AWStats)
*   Memoria consumida y procesada en APC por cada dominio.
*   ...vendrán más :-)

[![Métricas de Plesk en Datadog](http://israelviana.es/wp-content/uploads/2012/12/plesk-datadog-metrics.png)](http://israelviana.es/wp-content/uploads/2012/12/plesk-datadog-metrics.png)

Un ejemplo de la utilidad de estas métricas es poder cotejar el tráfico de cada dominio con su consumo de memoria en APC y el consumo general de los procesos de Apache, para aproximar cuánto consumen los clientes y estimar cuántos clientes más puedo llegar a soportar.

Los scripts para [enviar métricas de Plesk a Datadog](https://github.com/isra00/plesk_datadog_metrics) (escritos en PHP) los he publicado en GitHub, cómo no. Para ponerlos a funcionar, simplemente introduce tus API keys de Datadog y crea los cronjobs para cada una de las métricas que quieras recolectar. Más información en el [README del proyecto](https://github.com/isra00/plesk_datadog_metrics).