
gvSIG basic architecture
========================

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

.. _`org.gvsig.tools.library` : https://gvsig.org/web/reference_catalog/lookupObject?uuid=603a7a0d9e4f0f0306401bd3c238a89d

.. _`org.gvsig.tools.service` : https://gvsig.org/web/reference_catalog/lookupObject?uuid=0aa6c6f67612c991f723fd8f4e069937

.. _`org.gvsig.tools.locator` : https://gvsig.org/web/reference_catalog/lookupObject?uuid=f628030a648208d04db33e339ba79176


Framework de plugins, Andami
----------------------------
*Andami* es el framework que utiliza gvSIG para componer la aplicación.
Principalmente provee dos tipos de servicios, por un lado de gestión y
carga de *plugins* y por otro, la gestión de ventanas que componen el interfaz 
de usuario.

En este punto vamos a centrarnos un poquito en la gestión de *plugins*.
*Andami* proporciona un lanzador para la aplicación que inicializa el 
sistema de *plugins*, y se encarga de cargar los *plugins* que están
instalados en la aplicación. Un *plugin* es una unidad funcional que 
puede proveer:

- Opciones de menús a instalar en la aplicación
- Nuevos botones y barras de herramientas
- Nuevos proveedores de datos 
- Nuevos tipos de documentos

Normalmente, en una instalación de gvSIG, encontraremos una carpeta *gvSIG/extensiones*. Esta carpeta contiene dentro los distintos *plugins* que están
instalados en la aplicación. Cada *plugin* está contenido en una carpeta, y dentro
de esta, nos encontraremos, siempre, los ficheros:

- **config.xml**. Es obligatorio que exista. Identifica la carpeta como un plugin. En el encontraremos información sobre:

  - Dónde encontrar las clases de ese plugin

    ..  tip::
        Puede encontrar más información sobre la estructura y
        distintas etiquetas que están permitidas en este 
        fichero en el documento `el fichero config.xml`_

    .. _`el fichero config.xml`: http://www.gvsig.org/web/reference_catalog/lookupObject?uuid=4137884c37a68ee8cd685248f7f12cc1

  - Que clases deben ser cargadas como *extensiones* (IExtension).
  - Si ese plugin depende de algún otro plugin
  - Qué opciones de menú añade el plugin a la aplicación
  - Qué barras de herramientas y botones añade el plugin.

  
- **package.info**, que informa sobre la versión, nombre, identificador
  o descripción del plugin. Sería algo así como el contenedor de los metadatos
  del plugin, y nos permite identificarlo para poder actualizarlo o 
  reinstalarlo.

Adicionalmente podemos encontrarnos una carpeta **theme**, en la que está la configuración 
del splash de la aplicación, iconos de las ventanas o colores de fondo.

Aunque el mecanismo para añadir nueva funcionalidad a gvSIG es aportando nuevos
plugins, estos a su vez contendrán una o más *extensiones*, siendo estas extensiones
las que implementarán la funcionalidad a aportar. Así pues, tendremos que un 
*plugin* contendrá *extensiones*.

Relacionado con el manejo de *plugins* las principales piezas que nos vamos a
encontrar son:

- **PluginsLocator**, que nos dará acceso al *manager* de plugins a través de un
  método estático *getManager*.

- **PluginsManager**, que nos proporciona métodos para interrogar sobre qué
  plugins hay cargados y qué extensión tienen cada uno de estos.

- La clase **PluginsServices** que representa la instancia de un plugin dado.

- El interface **IExtension**, que representa una acción o bloque de acciones
  a incorporar a gvSIG.

- El interface **ExclusiveUIExtension**, que nos permite controlar la visibilidad
  de todas las extensiones cargadas en la aplicación.

De estas entidades nos vamos a detener un poco más en la interface **IExtension**- Esta
es la interface que deberemos implementar para poder añadir un nuevo botón u 
opción de menú. En esta interface nos encontraremos los métodos:

- **Initialize**, este método se ejecuta cuando se carga la extensión, en la
  inicialización del plugin. Hay que tener en cuenta que cuando se invoque
  a este método es posible que no hayan sido cargadas e inicializadas todas
  las extensiones de gvSIG. Normalmente aquí realizaremos la inicialización 
  de componentes de nuestra extensión que deban ser usados por otras extensiones.

- **postInitilize**, se invoca en la indización la la extensión. Justo despues 
  de haber invocado al método *initialize* de todas las extensiones de gvSIG.
  Normalmente, aquí realizaremos la inicialización de componentes de nuestra
  extensión que requieran de otras extensiones.

- **terminate**, se invoca cuando gvSIG precisa descargar esa extensión. Normalmente
  esto se producirá al cierre de la aplicación. Aquí deberemos asegurarnos que sean
  liberados los recursos del sistema que tenga reservados nuestra extensión.

- **execute**. Este método se ejecuta cada vez que el usuario interactúa con el 
  menú o botón de la barra de herramientas asociado a la extensión. Normalmente
  será donde se implemente la ejecución de nuestra funcionalidad.

- **isEnable**, es invocado cuando se precisa saber si el interfaz de usuario
  asociado a la extensión (botones u opciones de menú) deben estar habilitados.

- **isVisible**, es invocado cuando se precisa saber si el interfaz de usuario
  asociado a la extensión (botones u opciones de menú) deben estar visibles.


Ventanas
--------

..  tip::
    Aunque actualmente no existe documentación relacionada
    con esto para la versión 2 de gvSIG, en general, la
    documentación existente para gvSIG 1, será válida.
    Puede encontrarla en `Las ventanas de Andami`_

.. _`Las ventanas de Andami` : http://www.gvsig.org/web/reference_catalog/lookupObject?uuid=cb84e3369a85f0dcac645716079455d5


*Andami*, además de proveer un mecanismo para la carga y gestión de *plugins*
dispone de un API para la gestión de las ventanas de la aplicación. Actualmente 
el paradigma usado para la gestión de ventanas es MDI. Sin embargo el propio
gestor de ventanas de gvSIG puede ser sustituido para proporcionar otras formas
de visualización (SDI, tabs,...).

Las principales entidades que vamos a encontrar en *andami* relacionadas con 
el manejo de ventanas serán:

- **MDIManagerFactory**, es la factoría donde se registra y de donde se obtiene
  la instancia del MDIManager (hace de Locator).
  
- **MDIManager**, representa al gestor de ventanas registrado en gvSIG. Tiene métodos
  para presentar o cerrar una ventana.

- **IWindow**, es el interfaz que debe implementar un JPanel para que se presente
  como una ventana en gvSIG.

- **WindowInfo**, lo suministran nuestros paneles a través del interfaz *IWindow*
  y define las características de nuestra ventana (título, tamaño, tipo,...).

En general siempre que no precisemos de un control fino sobre nuestras ventanas
podemos usar el método *showWindow* del *MDIManager* para presentar una ventana
a partir de un *JPanel* estándar de swing.



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
el nombre del proyecto de Eclipse, por el nombre del jar generado o por el 
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
    Aunque redactadas para gvSIG 2.0.0, existe documentación sobre las librería s
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


