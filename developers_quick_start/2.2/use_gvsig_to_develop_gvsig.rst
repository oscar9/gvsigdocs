Usar gvSIG frente a modificar gvSIG
===================================

Hasta la versión 2 la principal forma de desarrollar con 
gvSIG era creando un workspace con los fuentes de la distribución oficial, con lo que para realizar cualquier plugin debían
tenerse los conocimientos necesarios para compilar la distribución de
gvSIG completa. Aunque se hicieron algunos esfuerzos para poder
desarrollar plugins sin necesidad de disponer de un workspace
de gvSIG completo, no es hasta la versión 2, que ese mecanismo 
funciona correctamente, de forma que la recomendación encarecida es
que nunca meta sus plugins en un workspace con los fuentes de gvSIG.

En la versión 2, la forma correcta de trabajar para desarrollar
un nuevo plugin, es sobre los binarios de una instalación de gvSIG,
creando un workspace únicamente con nuestros proyectos, y 
desplegando nuestros binarios sobre esa instalación.

Además de esto, en la versión 2, se ha hecho un esfuerzo considerable 
por independizar las librerías que usa gvSIG del mecanismo de plugins,
lo que nos permite realizar gran parte del desarrollo sin
necesidad de enalzar con gvSIG, ni para compilar ni para probarlo.
Podemos generar pequeñas aplicaciones que usando las librerías de 
gvSIG se puedan ejecutar de forma independiente.

Complementando esto, se han introducido los cambios necesarios
para que no precise modificar el código para hacer las operaciones
habituales que pueda necesitar para hacer su desarrollo.

Si ya ha desarrollado para gvSIG 1, deberá tener muy presente
este cambio en el modo de trabajo. No intente crear un workspace
con los fuentes de gvSIG y compilarlo si no lo necesita, cosa que
solo sucedera si precisa hacer modificaciones en ellos. 
En general, no precisará modificar los fuentes de gvSIG, pudiendo
desarrollar su funcionalidad en su propio plugin en lugar de modificar 
el código de gvSIG, y si precisa hacerlo y sus modificaciones pueden 
ser de utilidad para el resto de usuarios puede enviarlas como parches 
para que se valore su inclusión en proximas versiones de gvSIG.
