
================================================================
Aportar un paquete al repositorio de paquetes de gvSIG
================================================================


:versión: 1.3

Alcance
========

Este documento especifica el procedimiento a seguir para añadir
un nuevo paquete al repositorio de paquetes del proyecto gvSIG,
tanto si se trata de un paquete alojado en servidores del
proyecto como si no.

Exclusiones
===============

Este documento no cubre:

- Qué debe cumplir un proyecto para que se incluya en la
  *distribución oficial de la aplicación gvSIG*. El paquete
  suministrado se incluirá en el repositorio web de paquetes
  de gvSIG pero no tiene porque distribuirse con los binarios
  de la distribución oficial.

- Qué debe cumplir un proyecto para que se considere un proyecto
  oficial gvSIG. Puede encontrar documentación al respecto en
  el documento `Proyectos oficiales en gvSIG`_

- Modificaciones o contribuciones a código ya existente en gvSIG.

  Si desea aportar código a un proyecto ya existente en gvSIG consulte
  el documento `Contribuciones y parches al código de gvSIG`_.

- Como solicitar al proyecto que albergue nuestro paquete.
  El responsable y creador del paquete deberá alojarlo en un servidor
  publico con acceso a el por http, entregando al proyecto solo
  el archivo *gvspki* con los metadatos del paquete.

Requerimientos
===============

Para añadir un paquete al repositorio de paquetes del proyecto gvSIG deberá
generar el paquete de instalación y suministrar el fichero *".gvspki"* generado.


El paquete deberá cumplir:

* Estar liberado bajo licencia GPL_v3_ o compatible.

* No podrá reemplazar ficheros de fuera de la carpeta del plugin
  que se está instalando. En caso de ser necesario se comunicará y
  evaluará esta necesidad.

* Deberá suministrarse información sobre el código y versión del
  paquete.

* Deberá suministrarse el nombre y descripción del paquete en inglés.

* Deberá suministrarse el nombre del propietario.

* Deberá suministrarse la url que permita acceder a los fuentes
  a partir de los que se generó.

* Deberán estar correctamente rellenados todos los datos del paquete
  en el fichero *package.info*.

* El nombre del paquete deberá seguir la nomenclatura usada para generar
  los paquetes con las herramienta de construcción del proyecto.

Además de estos requerimientos es muy recomendable:

- La existencia de documentación de usuario asociada a la funcionalidad
  aportada por el paquete. Esta documentación deberá estar liberada con
  licencia `Creative Commons` .

- Que exista un tracker en el que comunicar bugs a los creadores de este.

Exclusión de un paquete del repositorio
============================================

Esta sección describe motivos por los que un paquete puede no ser incluido
en el repositorio de paquetes de gvSIG o eliminado en caso de que ya formase
parte de el.

- Actualización del contenido de un paquete sin incrementar el numero de
  version o build.

  Cada vez que cambie el plugin, deberá generarse un nuevo paquete reflejándose
  esto como mínimo en el aumento del número de build, enviando de nuevo al proyecto
  el fichero *gvspki*. Si desde el proyecto se detecta que ha cambiado el paquete
  manteniendo el mismo número de versión y build, se retirará el paquete del
  repositorio.

- Si desde el equipo de gvSIG se realizan consultas a través
  de los comentarios al ticket en el que se solicito el alta del paquete
  que no son contestadas, podrá eliminara el paquete del repositorio
  o no introducirse en el si todavía no se ha hecho.

- Si se comprueba que el uso de las funcionalidades aportadas por el paquete puede
  provocar la perdida de datos por parte del usuario de estas, este podrá
  ser eliminado del repositorio de paquetes.

Cómo iniciar los tramites
============================

Si ya ha generado el paquete de instalación asociado a su
plugin (.gvspkg y .gvspki) podrá solicitar la inclusión en el repositorio
de paquetes del proyecto gvSIG mediante un ticket en el trac `gvSIG add-on request`_ .
Para esto deberá estar dado de alta como un usuario de la plataforma_gvSIG_ .

Deberá añadir un ticket rellenado los campos:

* **Subject** deberá indicar aqui que desea añadir un nuevo paquete, con su código y
  versión siempre que sea posible.

* **add-on operation requested**. Debera indicar el tipo de operacion que esta
  solicitando.

* **Description**. Indique aqui la url al fichero *gvspki* de su paquete. Ademas indique la informacion que considere pueda ser de interes para facilitar la aceptacion del paquete, asi como su inclusion en el repositorio de paquetes.

Asegurese que la entrada *download-url* del *package.info* del paquete que suministra
es correcta y accesible desde internet, si no el paquete no podra ser incluido en
el repositorio de paquetes de gvSIG.

No asigne el ticket a nadie y deje el resto de campos con los valores
por defectos que tenga el tiquet.

Tenga en cuenta que si desde el proyecto se tiene alguna duda sobre
la petición que ha realizado se le contestara en el mismo ticket añadiendo
comentarios.



.. _GPL_v3: https://www.gnu.org/licenses/gpl-3.0.html
.. _`Creative Commons` : http://creativecommons.org/
.. _plataforma_gvSIG : https://devel.gvsig.org/redmine
.. _`Contribuciones y parches al código de gvSIG` : ../Contribuciones_al_codigo/Contribuciones_y_parches_al_codigo_de_gvSIG.html
.. _`Proyectos oficiales en gvSIG` : ../Proyectos_oficiales/Proyectos_oficiales_en_gvSIG.html
.. _`gvSIG add-on request` : https://redmine.gvsig.net/redmine/projects/gvsig-desktop/issues/new?issue[tracker_id]=8

.. list-table:: Registro de cambios
   :header-rows: 1

   * - versión
     - Descripción

   * - 1.1
     - Cambios en la seccion *Cómo iniciar los tramites* para adaptarla a la nueva
       infraestructura de gvSIG (redmine).

   * - 1.2
     - Cambios en la seccion *Cómo iniciar los tramites*, se ha cambiado la forma en como se proporciona el fichero gvspki, de adjuntarse al ticket a poner su url en la descripcion.

   * - 1.3
     - Adaptación a la nueva infraestrucura del proyecto.
     
       Cambiada la preferencia por obligatoriedad a en la nomenclatura del paquete.


