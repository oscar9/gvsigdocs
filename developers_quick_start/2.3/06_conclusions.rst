 
Conclusiones
============

Hemos visto de forma muy rápida cómo crear nuestro proyecto de desarrollo de una
funcionalidad para gvSIG 2.3.1. No se trata solo de un desarrollo para gvSIG, en
realidad podemos observar que se trata de una forma ordenada de desarrollar, separando
las distintas capas de abstracción que podemos encontrar en el desarrollo:

- lógica de negocio 
- Interface de usuario
- Integración con gvSIG

Y para cada una de ellas marcando claramente qué forma parte de nuestro API y qué
es implementación.

No solo aportamos una funcionalidad a gvSIG, si no que ofrecemos la funcionalidad
para que pueda ser reutilizada por otros desarrollos a través de un API bien definido,
pudiéndonos beneficiar en otros proyectos de disponer de esa funcionalidad empaquetada
a modo de librería.

Además nos beneficiaremos de los mecanismos de Maven que nos permiten desplegar esas
librerías de forma que sean fácilmente accesibles para otros desarrolladores.

Por último resaltar que esta forma de trabajar nos permite además poder desplegar 
fácilmente nuestros plugins ya que se reducen las dependencias entre plugins al 
atacar directamente a las librerías que necesitemos.






