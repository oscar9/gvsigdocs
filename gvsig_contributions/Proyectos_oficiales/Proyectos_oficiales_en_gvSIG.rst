
============================
Proyectos oficiales
============================

:Version: 1.13


Alcance
========

Este documento cubre los requerimientos que debe
cumplir un desarrollo para pasar a ser un *proyecto
oficial de gvSIG*.


Exclusiones
===============

Este documento no cubre:

- Qué debe cumplir un proyecto para que se incluya en la
  *distribución oficial de la aplicación gvSIG*.

  Para que se incluya en la distribución oficial de gvSIG deberá
  ser un *proyecto oficial gvSIG*, pero que cumpla este requerimiento
  no lo garantiza de forma automática.

- Modificaciones o contribuciones a código ya existente en gvSIG.

  Si desea aportar código a un proyecto ya existente en gvSIG consulte
  el documento `Contribuciones y parches al código de gvSIG`_.


Niveles de oficialidad de contribuciones de desarrollo en gvSIG
==================================================================

Antes de ver estos requerimientos, hay que concretar que existen tres niveles de
oficialidad que puede tener un desarrollo:

* **Nivel básico**. Se integra el testeo y documentación de usuario. *El proyecto
  gvSIG* no se hace cargo de mantenerlo, ni de corrección de bugs, añadir nuevas
  funcionalidades o adaptarlo a una nueva versión.

  Si se decide incluir en una distribución oficial y para una nueva versión
  no compila o falla en tiempo de ejecución o hace que
  otra extensión falle, simplemente se comunicará al contribuidor que aporta la
  extensión y si no dedica recursos a ello **se retirará de la versión**.

  Este nivel apenas cubre restricciones de empaquetado, documentación, y testing
  de usuario, no exigiendo que se cumplan ninguna de las normas o
  recomendaciones respecto a diseño e implementación, con lo que mantener el
  desarrollo para los miembros que estén trabajando con gvSIG puede tener un
  coste bastante elevado.

* **Nivel medio**. La contribución se integra tanto a nivel de testeo y
  documentación de usuario como de desarrollo. Desde el proyecto gvSIG
  se verifica que se cumplen unos mínimos requisitos para integrarse dentro
  del mecanismo de construcción usado por el proyecto gvSIG en caso de que
  fuese requerido. Es preciso tener este nivel de certificación para que
  desde la Asociación gvSIG nos planteemos abordar la corrección de pequeños
  bugs o adaptaciones a nuevas versiones de gvSIG; pero esto siempre dependerá
  de la disponibilidad de recursos.

* **Nivel completo**. La contribución se integra tanto a nivel de testeo y
  documentación de usuario como de desarrollo. Se verifica que el desarrollo
  esta correctamente estructurado, existe una separación de conceptos acorde
  a lo usado por en gvSIG, las APIs están documentadas y existe una mínima
  documentación de desarrollo y se siguen las normas de codificación de gvSIG.
  Es preciso tener este nivel de certificación para que desde la Asociación
  gvSIG nos planteemos aportar recursos para su mantenimiento o añadir nuevas
  funcionalidades; pero esto siempre dependerá de la disponibilidad de recursos.


El primer nivel se diferencia principalmente de los otros dos en que fija requerimientos
relacionados con el empaquetado y distribución de un proyecto, mientras que en los
otros entra ya en temas relacionados con el desarrollo (análisis, diseño, codificación,
documentación para desarrolladores,...)

Requerimientos para un desarrollo oficial de gvSIG
=============================================================

Los requerimientos que fija cada nivel deben interpretarse como acumulativos,
siempre que no se diga lo contrario en lo que a algún requerimiento se refiere.
Esto quiere decir que para estar en el *nivel completo* debe cumplir los del *nivel
básico* y *nivel medio*.

De los requerimientos a cubrir por un proyecto para pasar a ser un proyecto
oficial de gvSIG, algunos deberán cubrirse al inicio del proceso de validación o
certificación mientras que otros podrán irse cubriendo a lo largo del proceso.


Nivel básico
--------------

Requerimientos al inicio del proceso de certificación:

* Deberá suministrarse una persona de contacto con capacidad para
  toma de decisiones en todo lo referente al desarrollo.

* La licencia del desarrollo debe ser compatible `GPL_v3`_ o posterior
  y no debe usar ninguna otra
  librería que sea incompatible con esta licencia.

  En cuanto a la documentación entregada junto con el proyecto para conseguir
  el reconocimiento de proyecto oficial, deberá usarse una licencia
  compatible `GPL_v3`_ o posterior o una licencia `Creative Commons`_ que
  permita el uso comercial de esta asi como su modificación manteniendo
  las mismas restricciones de redistribución que la documentación
  original.

* Deberá entregarse un informe de dependencias de la librería o plugin, en el
  que se indique de qué librerías depende y su versión. Este informe debe
  contemplar las dependencias con otros plugins y librerías de gvSIG,
  estando estas dependencias bien identificadas.

  Así mismo también se indicarán las dependencias con librerías nativas.

  Este documento deberá mantenerse actualizado a lo largo de la vida del
  proyecto.

  Si el proyecto usa maven como mecanismo de construcción bastará con
  entregar el informe de *dependencias* del sitio maven generado con
  *mvn site*. En caso de no usar maven se utilizará la plantilla
  `Project Dependencies`_.

* Deberá existir una forma inequívoca de identificar una revisión del
  desarrollo. Normalmente si es una librería deberá llevar un número de versión
  en el nombre del *jar*, y si es un plugin un número de *build* único para
  ese plugin. No deberán existir dos binarios distintos con el mismo nombre y
  número de versión.

* Los fuentes a partir de los que se generen los binarios y las herramientas
  necesarias para generarlos deberán estar de acceso
  público

  Actualmente gvSIG está usando un redmine propio como espacio para el proyecto
  en el que se incluye un repositorio de SVN de acceso público en modo lectura,
  *trackers* para errores y tareas, etc. Si el proyecto no dispone de
  infraestructura para su gestión, desde gvSIG se puede gestionar la creación de un nuevo
  proyecto para el colaborador.

* Cuando se descarguen los fuentes desde el repositorio de fuentes deberá
  existir en el raíz del proyecto un fichero `README.txt`_ que describa qué hay
  que hacer para compilar, desplegar y generar el instalable.

Los requerimientos a cubrir a lo largo del proceso de certificación son:

* Deberá existir una `documentación de análisis`_ que describa
  el desarrollo.

  No es preciso llegar a nivel de diseño, pero sí es imprescindible
  que exista un catalogo de requisitos así como una mínima documentación
  de análisis. Grandes módulos y relación de estos con otros módulos de gvSIG.

* Deberá suministrarse una forma repetible y documentada de **generar
  instalables** para la extensión en gvSIG. Se preferirá que se use
  el procedimiento al uso para la versión corriente de gvSIG para
  empaquetar los plugins.

* Deberá ser internacionalizable usando las librerías de gvSIG para
  ello. Se entregará como mínimo en **inglés**, pudiéndose obviar la
  entrega en ingles cuando quede claro que la solución que aporta el
  desarrollo es solo de utilidad para su uso local.

* Deberá de entregarse un manual de usuario, en formato ODT de LibreOffice
  (en inglés y/o español), intentando seguir el estilo empleado en el manual
  oficial de gvSIG. La documentación de usuarios a entregar deberá cubrir:

  - Documentación de uso.
  - Documentación de instalación (en caso de que requiera alguna instalación especial).
  - Créditos (autores de la documentación).

* Debera suministrarse un instalable del proyecto.

  Si es un plugin para gvSIG deberá consistir en un paquete de instalación
  para este, siguiendo las reglas de nombrado usadas por las herramientas
  de construcción usadas por el proyecto gvSIG.

* En lo que a **testing** se refiere los requerimientos serian:

  1. Deberá diseñarse un Plan de Pruebas (PDP) que cubra por entero las nuevas funcionalidades, como mínimo hasta el nivel de caso de prueba. Se tendrán en cuenta tanto las pruebas funcionales como las de persistencia. El PDP se introducirá en la aplicación de gestión de Planes de Prueba del proyecto y se realizará una primera ejecución desde la misma. Podrá solicitarse también la ejecución de una campaña de pruebas, que el Área de Testing diseñará ex-profeso, en función del análisis del impacto de los nuevos desarrollos sobre el resto de la aplicación, incluyendo pruebas de regresión y de persistencia. Recomendablemente se dará de alta un boletín de bug por cada error encontrado al ejecutar las pruebas, indicando el paso concreto en el que se ha detectado el error.

  2. Se abordará una fase de estabilización en la que un equipo de desarrolladores y otro de *testers* trabajarán de manera intensiva y coordinada en la corrección de errores, tanto en los propios de las nuevas funcionalidades como en los que haya provocado la integración de éstas en el resto de la aplicación.

  3. Cuando para una versión de gvSIG el desarrollo no pase el plan de pruebas este perderá la denominación de proyecto oficial para esa versión de gvSIG.

  4. Deberá existir un gestor de bugs y nuevas funcionalidades de acceso publico, permitiendo a usuarios anónimos que puedan dar de alta tickets en él.

  En caso de que no se disponga de uno se podría gestionar la creación de una en la infraestructura de gvSIG.

* Deberá haber un plugin de gvSIG por funcionalidad o grupo de funcionalidades
  relacionadas desde el punto de vista del usuario.

* Deberá existir una política clara y conocida en relación al mantenimiento
  del número de versión del proyecto.

* La instalación de un plugin en gvSIG no deberá sobre escribir ninguna de las
  librerías que se incluyan con la distribución oficial de gvSIG o de otros plugins.

Existen dos plantillas en formato ReST_ que deberán ser rellenadas y
entregadas con los datos básicos del proyecto, la `ficha del proyecto`_
y los `contactos del proyecto`_.

Nivel medio
----------------

Este nivel sólo aporta requerimientos a cubrir al final del proceso
de certificación.

* Se utilizara maven_ como entorno de construcción de la
  extensión o librería, utilizando la estructura
  de proyecto para gvSIG. Con los criterios de nombrado de
  paquetes, artefactos y librerías de gvSIG.

  Así mismo deberán estar correctamente cumplimentados los ficheros
  *pom.xml* incluyendo por ejemplo:

  * Descripción del proyecto.
  * Enlaces a repositorio de código.
  * Enlaces a listas de correo.
  * Desarrolladores.
  * Etc.

* Deberá seguir las `normas de codificación`_
  vigentes en el proyecto.

* La implementación de la lógica deberá estar en librerías
  independientes del framework de plugins de gvSIG (andami) y
  de cualquier plugin.

* La implementación del interface de usuario asociado a la lógica
  deberá estar en librerías
  independientes del framework de plugins de gvSIG (andami) y
  de cualquier plugin.

* Se valora muy positivamente la separación en librerías distintas
  para la lógica y para el interface de usuario.

* La integración con gvSIG se realizara a través de plugins que aportaran
  las librerías de lógica e interface de usuario.

Nivel completo
----------------

Este nivel sólo aporta requerimientos a cubrir al final del proceso
de certificación.

* Existirá una documentación completa del API a través
  de los *javadocs*.
  La documentación del API se redactará en **inglés**.

* Se valora la existencia de pruebas automatizadas,
  usando tests JUnit_ que cubran el API de la extensión o librería.

* Habrá una separación estricta entre la lógica y la parte de
  *interface* de usuario.

* Habrá una separación estricta entre API e implementación,
  generándose una librería para el API separada de la implementación.
  Tanto para la **lógica** como para el **interface de usuario**.

* Deberá confeccionarse una *guía para el desarrollador* que
  documente cómo usar las funcionalidades. El idioma en el que se
  confeccione esta guía será preferentemente el **inglés**.

* Deberá existir una versión de la documentación que permita la navegación
  mediante un navegador, preferiblemente en HTML.


Como iniciar los tramites
============================

Si está interesado en que su desarrollo sea un *proyecto oficial de gvSIG*
puede solicitarlo enviando un correo a info@gvsig.com .

Describa en unos pocos párrafos la funcionalidad que aporta su desarrollo.
Puede aportar como documentos adjuntos las plantillas de
`ficha del proyecto`_ y `contactos del proyecto`_ o hacerlo mas adelante
cuando se le solicite. Para rellenar las
plantillas recuerde hacerlo a partir del código fuente de estas.

A partir de esto se pondrá en contacto con usted la persona adecuada del
proyecto gvSIG para llevar la coordinación de las tareas de oficializar su
desarrollo en gvSIG.

.. _ReST: http://docutils.sourceforge.net/rst.html
.. _maven: http://maven.apache.org/
.. _JUnit: http://www.junit.org/
.. _GPL_v3: https://www.gnu.org/licenses/gpl-3.0.html
.. _`Creative Commons` : http://creativecommons.org/


.. _`normas de codificación` : ../normas_de_codificacion.html
.. _`documentación de análisis` : Documentacion_de_analisis.html
.. _`contactos del proyecto` : templates/contacts.html
.. _`ficha del proyecto` : templates/project_sheet.html
.. _`README.txt` : templates/README.txt.html
.. _`Project Dependencies` : Dependencias_del_proyecto.html
.. _`Contribuciones y parches al código de gvSIG` : ../Contribuciones_al_codigo/Contribuciones_y_parches_al_codigo_de_gvSIG.html

.. list-table:: Registro de cambios
   :header-rows: 1

   * - versión
     - Descripción

   * - 1.1
     - Añadido enlace a una plantilla para el *informe de dependencias*.

   * - 1.2
     - Sustituido el enlace a la plantilla del informe de dependencias
       por enlace a un documento que describe como rellenar la plantilla
       del informe de dependencias y de donde obtenerla.

   * - 1.2
     - Añadido enlace a un documento explicativo de como rellenar el
       README.txt a incluir con los fuentes y donde obtener la
       plantilla de este.

   * - 1.2
     - Añadido enlace a un documento explicativo de que debe entregarse
       como documentación de análisis para el nivel básico, así como
       enlace a un ejemplo.

   * - 1.2
     - Añadido enlaces a las plantilla de la *ficha del proyecto* y los
       *contactos*

   * - 1.2
     - Corregida referencia a nivel *básico* y *avanzado*. En un punto se
       referenciaban como *mínimo* y *alto*.

   * - 1.3
     - Rehecho el primer párrafo del documento para
       que exprese mejor lo que es un proyecto
       oficial de gvSIG.

   * - 1.4
     - Añadido requerimiento de que funcionalidades distintas van en
       plugins distintos.

   * - 1.5
     - Añadido requerimiento de política relacionada con la versión.

   * - 1.5
     - Añadido requerimiento de política relacionada con el nombrado de
       ficheros de una distribución.

   * - 1.5
     - Sustituido el requerimiento de seguir las normas de nombrado de
       clases por el de seguir las normas de codificación.

   * - 1.6
     - Añadido el epígrafe *"Como iniciar los tramites"*.

   * - 1.6
     - Correcciones ortográficas y de sintaxis.

   * - 1.7
     - Subdividida la sección *alcance* en dos, *alcance* y *exclusiones*.
       y añadido enlace a `Contribuciones y parches al código de gvSIG`_

   * - 1.8
     - No se deben sobre escribir librerías de gvSIG.

   * - 1.9
     - Añadidas condiciones sobre la licencia de la documentación
       entregada.

   * - 1.10
     - Añadidas la nota de que si no pasa el plan de pruebas en una versión
       pierde la denominación de proyecto oficial (punto 3 de requerimientos
       de testing).

   * - 1.10
     - Añadido que se requiere un gestor de bugs publico para el proyecto
       (punto 4 de requerimientos de testing).

   * - 1.10
     - Extendidas la descripción de que documentación de usuario debe entregarse,
       manual de uso, de instalación y derechos.

   * - 1.11
     - Añadido párrafo sobre que el fichero pom.xml debe estar correctamente
       cumplimentado.

   * - 1.12
     - Añadido requerimiento sobre el instalable a entregar para el proyecto o plugin.

   * - 1.13
     - Adaptado a la nueva infraestructura.
       Dividido el nivel completo en dos, medio y completo.

