
================================================
Contribuciones y parches al código de gvSIG
================================================

:Versión: 1.3

Alcance de este documento
=============================

Este documento pretende dar una guía de los pasos que hay
que seguir para introducir modificaciones a código ya existente en
gvSIG.

Las aportaciones al código de gvSIG las dividimos principalmente en
tres tipos:

#. *Bugs*. Correcciones de errores en las funcionalidades ya existentes
   en gvSIG.

#. *Mejoras en el acabado de una funcionalidad*. Se tratan de pequeñas
   mejoras sobre una funcionalidad ya existente en gvSIG.

#. *Nuevas funcionalidades*. Aporte de nuevas funcionalidades o modificaciones al código
   existente para poder desarrollar una nueva funcionalidad de
   forma externa.

Ya que estas tres formas de contribuir al código de gvSIG no siguen exactamente el
mismo cauce y tramites vamos a describirlas por separado.

Una cuestión común a todas las formas de contribuir al código de gvSIG es que
deberá ceder los derechos del código que aporte al proyecto gvSIG, tal como
se describe en los documentos `Que es la CLA de gvSIG`_ y `Contributor License Agreement`_.

Exclusiones
=============

Este documento no cubre los requerimientos que debe cumplir un
proyecto que aporte un nuevo plugin a gvSIG, para esto le recomendamos
que consulte `Proyectos oficiales en gvSIG`_.

Bugs
=======

Si ha detectado algún *bug* en la aplicación y ha implementado código
para corregirlo puede aportar este para que sea incluido en las próximas
versiones de la aplicación.

No siempre se aceptaran todas las correcciones, unas por falta de recursos
para incluirlas en la revisión, otras por desacuerdos sobre si esa implementación
es la adecuada o no para corregirlo. Hay que tener en cuenta que en
ocasiones la corrección de un *bug* a la ligera puede conllevar la aparición
de efectos no deseados en otras partes de la aplicación.

Deberá realizar dos tareas para aportar la corrección de un *bug*:

- Por un lado deberá dar de alta éste en el `registro de bugs de gvSIG`_.
  gvSIG usa como registro de *bugs* un *tracker* o *boletines* dentro
  de su `gestor de incidencias`_.

  Para dar de alta el *bug*, es deberá darse de alta como usuario
  de la infraestructura de gvSIG.

  Cuando dé de alta el *bug*, ponga especial interes en:

  - Utilize un **Subject** adecuado y que de idea sobre de que va
    el ticket.

  - Rellene el campo **gvSIG version** con el
    numero de version de gvSIG en la que detecto el problema.

  - Rellene el campo **gvSIG build** con el
    numero de build de gvSIG en la que detecto el problema.

  - Rellene el campo **Operative System** indicando en que plataforma
    ha detectado el error.

  - Rellene el campo **Severity**.

  - Marque el campo **Has patch** a **yes**, si va aportar un parche.

  - El campo **State** debera estar a **Open**

  - El campo **Resolution** al valor **Awaiting response**.

  .. tip:: Es muy importante que marque en el ticket el campo *Patch*
           ya que es usado por el equipo de desarrollo para identificar
           los *bugs* que aportan código.

  Cuanta más información aporte en la descripción del ticket sobre lo que
  realiza su código más facilitará la tarea del responsable de gvSIG
  encargado de evaluar el impacto que puede tener su corrección.

  Si después de evaluar la corrección del *bug* se considera adecuado
  introducirla en gvSIG, se le indicará a través del ticket que se ha
  realizado dicha tarea.

  En caso de que se considere *problemática* esa corrección se le informará
  también a través del ticket.

- Por otro lado, la primera vez deberá hacernos llegar el documento con la CLA_ cediendo
  los derechos de autor sobre el código que aporta a gvSIG. Puede obtener más
  información sobre esto en el documento `Contributor License Agreement`_.

.. important::

   Si la modificación la propone un usuario con permisos de *commit*,
   no será necesario que adjunte un parche, en su lugar, bastará con
   añadir un comentario con el número de revisión que aplica el cambio.

   Antes de subir los cambios, deberá esperar a que sea aprobado el ticket y
   se le asigne.

   *Esto implica que para todas las correcciones se deberá dar de alta
   el ticket correspondiente y ser aprobado y asignado*.


Recuerde que los recursos del proyecto gvSIG son limitados y no siempre
podremos actuar de forma inmediata sobre sus tickets. Intentaremos hacerlo
lo antes posible.

Mejoras en el acabado de una funcionalidad
============================================

Entendemos que a veces hay una serie de pequeños cambios que no siendo considerados
como errores o bugs estaría bien que se realizasen sobre gvSIG. Pueden ser cambios
tan simples como que la opción por defecto de un desplegable es tal que obliga a
cambiarla siempre que se accede a ese cuadro de dialogo o que la posición de una
opción de menú parece más lógica que deba estar en otro sitio. No son errores
propiamente dichos ya que la aplicación se comporta como fue diseñada. Realmente
se trata de pequeñas mejoras en una funcionalidad ya existente.

Los pasos a seguir para aportar este tipo de modificaciones al código serán
similares a los que ha de seguir la aportacion de código para un *bug*, con
la unica salvedad que en lugar de usar el `registro de bugs de gvSIG`_ deberá
usar el de `Nueva petición de funcionalidades`_ .

Nuevas funcionalidades
========================

A la hora de incluir una nueva funcionalidad en gvSIG deberá tener
en cuenta que, en general, cuando se deseen incluir nuevas funcionalidades
en gvSIG estas se incorporarán en forma de nuevos plugins; de
forma que añadir una nueva funcionalidad desde el punto de vista del
usuario, normalmente no deberá pasar por este tramite. La forma más
simple será añadir la nueva funcionalidad al catalogo no oficial de
gvSIG o seguir los tramites descritos en `Proyectos oficiales en gvSIG`_
para que se incorpore como un proyecto oficial.

Entonces... ¿Cuándo deberemos seguir estos trámites?

Deberemos aplicar este procedimiento cuando para añadir nuestra funcionalidad
tengamos que modificar partes de gvSIG, normalmente añadiendo nuevos puntos
de extensión. La funcionalidad desde el punto de vista del usuario
quedará en un plugin independiente, y se introducirán los cambios necesarios
en gvSIG para que el plugin pueda llevar a cabo su cometido.

Para aportar el código suficiente a gvSIG que nos permita desarrollar
nuestro plugin deberemos solicitarlo a través del *tracker* de
`Nueva petición de funcionalidades`_.
Allí daremos de alta un ticket con la descripción de lo que deseamos hacer,
adjuntando el código, en forma de parche o parches, con la implementación
de ésta.

Antes de ponernos a desarrollar nuestra funcionalidad deberemos dar de alta
el ticket, describiendo:

- En qué consiste la nueva funcionalidad a añadir a gvSIG. No se
  trata de la funcionalidad que quiere implementar en su plugin, sino
  la que necesita añadir a gvSIG para poder desarrollar su plugin.

- La motivación que le lleva a pedir la introducción de esa funcionalidad,
  para qué la necesita.

- Cómo planea abordarlo. Una descripción textual de cómo va a implementar
  la funcionalidad, qué módulos precisa tocar, por y para qué.

- Una descripción de a qué partes de gvSIG cree que puede afectar los
  cambios que propone.

En el ticket deberá marcar el campo **Has patch** a **yes** para que desde el equipo de
desarrollo identifiquemos claramente el ticket como una petición de funcionalidad
que pretende aportar código a gvSIG. Asi mismo debera dejar los campos
**State** y **Resolution** con los valores de  **Open** y **Awaiting response** respectivamente.

Tenga en cuenta que:

- Si su petición implica cambios en varias partes de la aplicación
  puede requerir la intervención de varios desarrolladores para evaluarlo.
  Esto puede ralentizar el proceso de inclusión de las modificaciones.

- Si se estima que su petición puede ocasionar perdidas de compatibilidad
  o de datos con proyectos de usuario, deberá revisar su propuesta, ya que
  esta, en general, no será aceptada.

- Una documentación deficiente a la hora de aportar su nueva funcionalidad
  puede ocasionar que esta no sea entendida por los responsables de
  evaluarla, con el consiguiente rechazo. Aporte toda la información que
  considere relevante para facilitar la tarea de evaluar lo que está pidiendo.

- Puede considerarse que su petición no es pertinente por que ya existan
  otras formas de hacer eso. Consulte en las listas de desarrollo
  antes de empezar a hacer cambios en gvSIG.

Una vez leída la petición, se le puede solicitar más información o que realice
algunos cambios en alguna parte de lo que pretende hacer, para por ultimo,
pedirle el código que lo implementa en forma de parche o parches, que deberá
adjuntar al ticket.

.. important::

   Si la funcionalidad la propone un usuario con permisos de *commit*,
   no será necesario que adjunte un parche, en su lugar, bastará con
   añadir un comentario con el número de revisión que aplica el cambio.

   Antes de subir los cambios, deberá esperar a que sea aprobado el ticket y
   se le asigne.


Recuerde que, la primera vez, deberá hacernos llegar el documento con la CLA_ cediendo
los derechos de autor sobre el código que aporta a gvSIG. Puede obtener más
información sobre esto en el documento `Contributor License Agreement`_.


.. _CLA : http://en.wikipedia.org/wiki/Contributor_License_Agreement
.. _`Proyectos oficiales en gvSIG` : ../Proyectos_oficiales/Proyectos_oficiales_en_gvSIG.html
.. _`Contributor License Agreement` : contributor-license-agreement.html
.. _`Que es la CLA de gvSIG` : Que_es_la_CLA_de_gvSIG.html
.. _`Nueva petición de funcionalidades` : https://redmine.gvsig.net/redmine/projects/gvsig-desktop/issues/new?issue[tracker_id]=7
.. _`registro de bugs de gvSIG` : https://redmine.gvsig.net/redmine/projects/gvsig-desktop/issues
.. _`gestor de incidencias` : https://redmine.gvsig.net/redmine/projects/gvsig-desktop


.. list-table:: Registro de cambios
   :header-rows: 1

   * - versión
     - Descripción

   * - 1.1
     - Añadidos los valores a los que deben dejarse los campos *state* y
       *resolution* de un ticket.

   * - 1.2
     - Cambiadas referencias de OSOR al nuevo https://devel.gvsig.org/redmine

   * - 1.3
     - Adaptacion a la nueva infraestructura de gvSIG


