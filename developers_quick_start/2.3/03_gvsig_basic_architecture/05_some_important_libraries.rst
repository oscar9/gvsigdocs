

Algunas librerías de relevancia
-------------------------------

A la hora de desarrollar con gvSIG, puede sernos útil conocer los plugins
existentes, sobre todo las funcionalidades que nos ofrece el *ApplicationManager*,
pero lo realmente importante es controlar qué librerías son las que vamos a
necesitar para implementar la funcionalidad que necesitemos. 

A partir de la versión 2.0.0 de gvSIG se introdujeron cambios importantes tanto en la forma
de construir gvSIG como en la nomenclatura utilizada para identificar los proyectos
y librerías que se generan. Sin embargo esto no ha sido un proceso que se dio 
de repente. Ha ido sucediendo en el transcurso de varios años. Esto ha dado como 
resultado una falta de homogeneidad en la nomenclatura de los componentes existentes
en este momento. A la hora de referirnos a una librería podemos hacerlo por
el nombre del proyecto en nuestro IDE, por el nombre del jar generado o por el 
nombre de artefacto de Maven. En los proyectos generados recientemente se optó como
norma que estos tres nombres debían coincidir, siendo el nombre que se adoptaba
para los tres el nombre del artefacto de Maven. Sin embargo proyectos anteriores
a esta decisión presentan nombres distintos para cada una de estas cosas. De cara
a un desarrollador que vaya a usar gvSIG, lo importante será conocer los
nombres de artefacto Maven, ya que normalmente deberá saber con qué artefactos
debe fijar las dependencias, siendo menos relevante el nombre del proyecto
asociado a ese artefacto.


Las librerías que más pueden cubrir nuestras necesidades en gvSIG serían:

- **org.gvsig.tools.lib**, contiene utilidades estructurales. Ya se comentó
  al inicio del documento, así que no nos vamos a extender en ella en este 
  punto.

- **org.gvsig.fmap.geometry**. Es la librería de manejo
  de geometrías de gvSIG. Presenta un modelo de geometrías con un API independiente
  de su implementación.

..  tip::
    Aunque redactadas para gvSIG 2.0.0, existe documentación sobre las librerías
    de acceso a datos y de geometrias que puede ser interesante de leer ya que casi en su 
    totalidad puede aplicarse a gvSIG 2.2.0. Puede encontrarlas  aqui la de 
    `DAL`_ y aqui la de `geometrias`_.

- **org.gvsig.projection**. Es la librería de manejo de sistemas
  de referencias coordenados de gvSIG.

- **org.gvsig.fmap.dal**. Es la librería de acceso a datos de
  gvSIG (*Data Access Library*). Es usada por todos los proyectos que de una forma
  u otra precisan acceder a un shape, dxf, una tabla de PostgreSQL, un GML,
  una capa de un servidor de WFS o un fichero DGN. Presenta un API común para acceso
  a datos vectoriales y tabulares o para acceso a coberturas.

- **org.gvsig.fmap.mapcontext**, contiene el API e 
  implementación de capas y mapa a nivel lógico.

- **org.gvsig.fmap.control**. Contiene componentes swing
  que se vinculan a los componentes lógicos que se exponen en la librería de DAL
  y mapcontext, como podría ser un componente visual para presentar un mapa
  o para presentar una tabla obtenida a partir de DAL.

- **org.gvsig.projection.jcrs**. Es una implementación del API de proyecciones
  de *org.gvsig.projection*.

- **org.gvsig.symbology.lib.api**. Contiene el API de la librería de simbología 
  de gvSIG.

- **org.gvsig.ui**. Se trata de una librería que alberga una
  serie de componentes gráficos de utilidad general.
  
  
.. _DAL: http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.fmap.dal/2.0.0
  
.. _geometrias: http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.fmap.geom/2.0.0


