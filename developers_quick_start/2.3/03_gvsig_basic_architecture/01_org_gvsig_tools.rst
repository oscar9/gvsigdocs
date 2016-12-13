
org.gvsig.tools
---------------
La librería *tools* es una pieza importante dentro de gvSIG.
Provee de una serie de utilidades y servicios en los que se apoyan el
resto de módulos de gvSIG. La principal funcionalidad que aporta está
relacionada con:

- Registro de puntos de extensión
- Inicialización de librerías 
- Utilidades para la separación entre API e implementación,
  (registro de APIs, registro de implementaciones para las APIs,
  registro de proveedores de servicios SPI o localización de una 
  implementación de un API)
- Monitorización de tareas

Aunque estos son los servicios más importantes que provee la librería
además aporta otras utilidades que se pueden considerar estructurales
a la aplicación. Como pueden ser:

- Disposables
- Interfaces para manejo de Observers-observables
- Interfaces para el manejo de visitors
- Definición de tipos de datos
- Interfaces base para evaluadores
- Persistencia de objetos

..  tip::
    
    Puede encontrar información mas detallada sobre como funciona
    el mecanismo de librerias de gvSIG en
    org.gvsig.tools.library_ , sobre los el uso de *locators*
    podemos consultar en org.gvsig.tools.locator_ , y sobre
    *managers* podemos encontrar documentación en
    org.gvsig.tools.service_ .


Vamos a quedarnos ahora únicamente con tres conceptos básicos que se van a repetir
a lo largo del código que vayamos viendo, y están ligados a las utilidades que nos
van a permitir separar de forma estricta el API de la implementación:

- **Librerías**. Se trata de piezas de código asociadas normalmente a un *jar*
  que aportan una funcionalidad. Cada una de estas librerías deberán tener
  una clase que implementa el interface *Library*, normalmente extendiendo
  la clase *AbstractLibrary*. Poseen métodos para inicializar la librería que son
  invocados en la carga de la misma.

- **Managers**. Se trata de componentes que aportan una funcionalidad a la
  aplicación, haciendo las veces de factoría para los distintos servicios
  que ofrece la librería, así como de configuración de estos. Actúan a modo
  de *singleton*, ya que normalmente en la aplicación solo existe una instancia
  para cada uno de estos componentes. Toda librería tendrá uno a más de estos
  según la lógica de esta.
  
- **Locators**. Se trata de una utilidad que nos permite registrar implementaciones
  de *managers* para una librería dada, así como recuperar una implementación de
  *manager* de un API en concreto.

Estas tres piezas deberemos tenerlas muy presentes siempre que queramos
usar o crear nuevos componentes ya que son los artefactos que nos van a permitir
el acceso a las distintas piezas de gvSIG.

.. _`org.gvsig.tools.library` : http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.tools/2.1.0/org-gvsig-tools-library

.. _`org.gvsig.tools.service` : http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.tools/2.1.0/org.gvsig.tools.service

.. _`org.gvsig.tools.locator` : http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.tools/2.1.0/org-gvsig-tools-locator


