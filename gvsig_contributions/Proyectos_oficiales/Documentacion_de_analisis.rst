
===================================================================
Documentación de análisis para proyectos oficiales de nivel básico.
===================================================================

:Version:       1.1


Cuando estemos suministrando la documentación de análisis para un
proyecto oficial de gvSIG de *nivel básico* tendremos que cubrir los
siguientes *apartados*:

* *Introducción*.

  Deberemos aportar un documento titulado "Introducción" con una
  breve descripción del proyecto, indicando qué se persigue
  cubrir y qué no.

  Esta introducción deberá constar de tres o cuatro párrafos
  describiendo de forma concisa los objetivos a cubrir.

* *Contexto*.

  Presentará un diagrama de bloques que nos situe los componentes
  de nuestro proyecto en relación a gvSIG y el modelo de capas
  de esta , *interface de usuario/aplicación gvSIG*,
  *lógica geo/fmap*, *librerías de apoyo*.

* *Visión general*.

  Presentaremos una visión general de los principales componentes que
  forman nuestro proyecto y las relaciones que hay entre ellos.
  Nos centraremos principalmente en los componentes que conforman el
  API de nuestro proyecto, obviando los componentes específicos de
  la implementación. Así mismo indicaremos que relación hay entre
  estos y los componentes de gvSIG.

  Normalmente tendremos:

  * Una breve descripción textual del modelo y sus componentes, unos
    cuantos párrafos.

  * Un diagrama que nos muestre de forma gráfica las relaciones entre
    los componentes, distinguiendo claramente entre los componentes
    de nuestro proyecto y los ya existentes en gvSIG.

  * Una breve explicación de cada uno de los componentes que aparecen
    en el diagrama. Normalmente debería bastar con un párrafo explicativo
    por cada uno de los artefactos del modelo.

  * Si el diagrama es muy complejo, este agrupará funcionalidades
    en componentes más generales, de forma que un componente en este
    diagrama podrá descomponerse en otros para una mayor comprensión
    del modelo. En este caso se incluirá una lista de los componentes
    en los que se *explotará* nuestro modelo.

* Visión detallada por componente.

  Para cada uno de los componentes que se haya especificado en la
  visión general que puede ser *explotado*, se incluirá de nuevo
  una descripción junto con un diagrama, de forma similar a lo realizado
  con la *Visión general*, pero para el componente en concreto con
  el que tratemos.

Se entregará un documento por cada *apartado*, presentando
tantos apartados de detalle como requiera nuestro proyecto.


Es recomendable seguir algunas convenciones como:

- Normalmente los distintos componentes en los diagramas no contendrán
  especificados atributos o métodos, ya que se trata de documentación de
  análisis y no de diseño, y incluirlos a este nivel nos llevaría a dificultar
  la comprensión del modelo.

- El diagrama se leerá de arriba a bajo y de izquierda a derecha.
  Con esto tendremos siempre, por ejemplo, en la herencia las super-clases
  en la parte superior y las subclases en la parte inferior, o en las
  relaciones unos a muchos, la entidad maestra arriba y la de detalle
  abajo o a la derecha.

- Se intentarán componer los diagramas de forma que sean más altos
  que anchos, intentando disponer sus componentes a lo alto y no
  a lo ancho. Esto es para que si se imprime la documentación estos
  no pierdan mucha calidad al ser escalados para adaptarlos a las medidas
  de un A4.

- No se escalarán los diagramas para incluirlos en la documentación. Si
  estos son demasiado grandes, se descompondrán en otros diagramas.

- Intentaremos que se crucen el mínimo número de líneas posible, así
  como no superpondremos unas con otras.

- Intentaremos usar códigos de color de fondo en las entidades para
  agrupar conjuntos de funcionales de entidades, suministrando una
  leyenda que nos permita identificarlas.

- Usaremos estereotipos para identificar a los interfaces y clases
  abstractas.

- Además, si acompañamos a nuestra documentación de análisis de los
  javadocs de nuestra implementación, intentaremos que cuando nos
  refiramos a una entidad, utilizar un enlace al javadoc de esta.

- Cuando se inserten los diagramas en la documentación, estos
  estarán en formato *png* con una paleta de colores reducida
  (paleta de 16 o 32 colores), para evitar que el documento
  tenga un peso excesivo para ser consultado por la web.

En el caso de que nuestro proyecto aporte alguna funcionalidad
que pueda ser usada por otros proyectos en forma de librería, se
aconseja presentar documentación de ilustre el uso de esas
funcionalidades.

En cuanto al formato a utilizar para la redacción de la documentación
es preferible el uso de ReST_, siendo admitido tambien en proyectos
de *nivel básico* el uso de HTML.

Podemos ver documentación de análisis similar a la que se requiere
se aporte en:

* `Documentación de análisis de DAL`_.

Es recomendable ponerse en contacto con un miembro del *grupo de arquitectura
de gvSIG* antes de confeccionar la documentación de análisis para comentar
qué es lo que se va ha hacer.



.. _`Documentación de análisis de DAL` : templates/ejemplo_de_analisis1.html
.. _ReST: http://docutils.sourceforge.net/rst.html





.. list-table:: Registro de cambios
   :header-rows: 1

   * - version
     - Descripcion

   * - 1.1
     - Se ha añadido enlace a como insertar imagenes en la documentación.

   * - 1.1
     - Se ha añadido la recomendacion de ponerse en contacto con el
       grupo de arquitectura de gvSIG antes de confeccionar la documentación.

