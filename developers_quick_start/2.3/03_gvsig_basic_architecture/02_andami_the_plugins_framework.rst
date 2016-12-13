
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
        Aunque actualmente no existe documentación relacionada
        con esto para la versión 2 de gvSIG, en general, la
        documentación existente para gvSIG 1, será válida.
        Puede encontrar esta información en la descripción del `fichero config.xml`_ 
        que hay para gvSIG 1.

    .. _`fichero config.xml`: http://docs.gvsig.org/plone/docdev/docs/v1_0/gvsig/andami/plugins-extensiones/fichero-config-xml

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

