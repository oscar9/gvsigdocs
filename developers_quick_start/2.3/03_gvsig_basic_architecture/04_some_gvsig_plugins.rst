

Algunos plugins de gvSIG
------------------------

En gvSIG, podemos encontrar una serie de plugins que aportan la funcionalidad base
de la aplicación que tienen cierta relevancia respecto al resto de plugins. Estos 
serían:

- org.gvsig.app.mainplugin_. Este plugin provee a gvSIG del grueso de la funcionalidad
  genérica de la aplicación, así como del acceso básico a datos vectoriales. Aporta:
  
  - El concepto de proyecto y sus documentos.
  - El documento View
  - Las herramientas básicas de acceso a datos vectoriales de la vista
  
  Este plugin además de la implementación de estas funcionalidades aporta un *manager*
  y un locator importantes de cara a desarrollar otros plugins:
  
  - **ApplicationManager**. Se trata de un manager que nos provee de acceso al
    resto de managers de la aplicación, así como de una funcionalidad básica para
    acceder a recursos globales de la aplicación. Vendría a ser el objeto
    que representa a la *aplicación* gvSIG.
    
  - **ApplicationLocator**, que es el locator que nos da acceso al manager de la
    aplicación (ApplicationManager).

    
- org.gvsig.app.document.table.app.mainplugin_

- org.gvsig.app.document.layout2.app.mainplugin_

- org.gvsig.vectorediting.app.mainplugin_, provee las funcionalidades de la edición de datos
  tabulares y vectoriales.

- org.gvsig.geodb.app.mainplugin_, añade a la aplicación soporte para acceso a BBDD, como
  PostgreSQL.

- org.gvsig.i18n.app.mainplugin_, añade el soporte para internacionalización a 
  la aplicación.

- org.gvsig.projection.app.jcrs_, añade soporte para gestión de CRSs.

- org.gvsig.raster.tools.app.basic_, añade soporte raster a gvSIG.


Estos pueden ser algunos de los plugins más importantes en gvSIG en el sentido 
de que ofrecen una funcionalidad base importante o que son necesarios para que otros
plugins puedan funcionar.

Además de estos plugins hay bastantes más plugins en la distribución de gvSIG
que pueden ser necesarios o no en función de lo que vayamos a hacer con gvSIG.

.. _org.gvsig.app.mainplugin: https://redmine.gvsig.net/redmine/projects/gvsig-desktop
.. _org.gvsig.app.document.table.app.mainplugin: https://redmine.gvsig.net/redmine/projects/gvsig-desktop
.. _org.gvsig.app.document.layout2.app.mainplugin: https://redmine.gvsig.net/redmine/projects/gvsig-app-document-layout
.. _org.gvsig.vectorediting.app.mainplugin: https://redmine.gvsig.net/redmine/projects/gvsig-vector-editing
.. _org.gvsig.geodb.app.mainplugin: https://redmine.gvsig.net/redmine/projects/gvsig-desktop
.. _org.gvsig.i18n.app.mainplugin: https://redmine.gvsig.net/redmine/projects/gvsig-desktop
.. _org.gvsig.projection.app.jcrs: https://redmine.gvsig.net/redmine/projects/gvsig-jcrs
.. _org.gvsig.raster.tools.app.basic: https://redmine.gvsig.net/redmine/projects/gvsig-raster

