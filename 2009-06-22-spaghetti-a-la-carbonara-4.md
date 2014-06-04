Spaghetti a la carbonara
========================

_22-06-2009_

He aquí un maravilloso ejemplo de [código espagueti](http://es.wikipedia.org/wiki/C%C3%B3digo_spaghetti) que he encontrado en... bueno, mejor correr un es-tupido velo.

<pre style="height: 200px; overflow: scroll" class="javascript">function revisar_email(correo) {
var nombre_valido = false;
var servidor_valido = false;
var dominio_valido = false;
var email = correo.value;
if (email != ""){
// se comprueba si ha metido algo en el campo
if (email.indexOf('@') != -1){
// se comprueba si contiene la @
var array1 = email.split('@');
if ((array1[0] != "") && (array1[1]) != ""){
// se comprueba que tenga caracteres delante y detrás de la @
if (array1[1].indexOf('.') != -1){
// se comprueba si tiene . detrás de la @
var array2 = array1[1].split('.');
if ((array2[0] != "") && (array2[1]) != ""){
// se comprueba si tiene 2 partes después de la arroba
var nombre = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-_.";
var dominio = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
var i, j;
for (i = 0; i <= array1[0].length; i++){
if (nombre.indexOf(array1[0].charAt(i)) == -1){
// revisamos que los caracteres del nombre sean correctos
nombre_valido = false;
break;
}
else{
nombre_valido = true;
}
}
for (i = 0; i <= array2[0].length; i++){
if (nombre.indexOf(array2[0].charAt(i)) == -1){
// revisamos que los caracteres del servidor sean correctos
servidor_valido = false;
break;
}
else{
servidor_valido = true;
}
}
if(array2[1].length > 1){
// revisamos que la longitud del dominio sea mayor a 1
for (i = 0; i <= array2[1].length; i++){
if (dominio.indexOf(array2[1].charAt(i)) == -1){
// revisamos que los caracteres del dominio sean correctos
dominio_valido = false;
break;
}
else{
dominio_valido = true;
}
}
}
}
}
}
}
if ((nombre_valido == true) && (servidor_valido == true) && (dominio_valido == true)){
// si todo es correcto devuelve true
return true;
}
else{
alert("El formato del email no es correcto.");
return false;
}
}
}</pre>

66 líneas para validar una dirección de email. Vaya, ¿y si usamos una **expresión regular**?

<pre lang="javascript">function revisar_email(correo) {
var filtro = /^([a-zA-Z0-9_.-])+@(([a-zA-Z0-9-])+.)+([a-zA-Z0-9]{2,4})+$/;
if (!filtro.test(correo)) {
alert('La dirección de correo electrónico no es correcta');
return false;
} else {
return true;
}
}</pre>

¡Hombre! ¡La misma funcionalidad implementada en 8 líneas! Moraleja:

*   El mal programador es malo en JavaScript, en Java y en cualquier otro lenguaje que toque.
*   Las expresiones regulares son nuestras amigas. Son feas y antipáticas, pero son nuestras amigas.
*   Dice Linus Torvalds que nunca deberían ser necesarios más de 3 niveles de anidamiento. Yo más bien pondría el límite en 5.
*   ¡Por Dios, no llames a tus variables array1 o array2! Utiliza nombres que tengan algún significado
*   Cuantas menos líneas de código, menos probabilidad de errores.
*   Cuantas menos líneas de código, más rápido será el algoritmo.
*   Cuantas menos líneas, más sencillo y fácil de comprender será el código para tus semejantes.
*   Las validaciones de formularios se hacen en el servidor. Por mucho JavaScript que haya puedo usar [Tamper Data](https://addons.mozilla.org/en-US/firefox/addon/966) para enviar directamente datos no válidos.