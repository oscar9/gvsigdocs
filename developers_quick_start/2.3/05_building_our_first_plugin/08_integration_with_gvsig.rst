

Integrándolo con gvSIG
----------------------


Hasta ahora hemos visto cómo crear nuestros componentes, lógica e interface de usuario, usando 
las librerías de gvSIG para acceder a los datos geográficos o para presentarlos, así como una
forma simple de crear una pequeña aplicación que los utilice. Vamos a ver ahora como integraríamos
esas funcionalidades en la aplicación gvSIG.

Si observamos los proyectos que tenemos en nuestro workspace veremos que aun hay uno sobre el que
no hemos trabajado, **org.gvsig.visor.app.mainplugin** . Es aquí donde está implementado nuestro 
plugin. Antes de ver el código del plugin comentar un detalle. Cuando describíamos lo que tenía
que hacer nuestro plugin, dijimos que debía presentar un *splash* personalizado. Veamos primero 
como podemos hacer esto.

En la carpeta *"src/main/resources"* encontraremos una carpeta *theme*, y dentro de esta un 
fichero *andami-theme.xml*. Este fichero es el encargado de especificar al framework de andami
que *splash* se debe presentar así como si hay que utilizar alguna imagen de fondo en el MDI
de la aplicación o los iconos de las ventanas de gvSIG. Andami, al arrancar, buscará en las 
carpetas de los plugins uno que tenga la carpeta *theme* y dentro este fichero y cuando
encuentre uno lo utilizará. El fichero xml de nuestro ejemplo contiene:

.. code-block:: xml

    <AndamiProperties>
        <ApplicationImages>
            <SplashImages>
                <Splash
                    path="splash.png"
                    timer="10000"
                    x="225" y="50"
                    fontsize="16" color="80,170,240"
                    version="gvSIG 2.3.1-2501 final (LandregistryViewer)"/>
            </SplashImages>	
            <!--	
            <BackgroundImage path="gvsig-proj-black-wallpaper.png"/>
            <BackgroundColor color="0,0,0"/>
            -->
            <WallpaperType value="CENTERED"/>
            <Icon path="$GVSIG_INSTALL/theme/gvsig-icon16x16.png"/>
        </ApplicationImages>
        <ApplicationName value="gvSIG 2.3.1-2501 final (LandregistryViewer)"/>
        <priority value="100"/>
    </AndamiProperties>    
    

Por defecto las rutas que aparezcan en el fichero se interpretarán relativas a 
la ubicación de este fichero, disponiendo de una variable *GVSIG_INSTALL* que 
apuntará a la carpeta en la que está instalado gvSIG. En el ejemplo podemos
ver como en el tag *Splash* no se indica ruta para el fichero *"splash.png"*,
usándose el fichero que hay en la carpeta del plugin mientras que en el tag
*Icon* se usa la variable *GVSIG_INSTALL* para hacer referencia al fichero
que hay en el tema por defecto de Andami.

Podemos arrancar gvSIG y comprobar que sale el *splash* indicado en 
nuestro fichero *andami-theme.xml*.

Una vez visto como podemos cambiar el *splash*, podemos echar un vistazo a la
extensión de nuestro plugin.

Vamos a ver ahora como se añaden menus y botones a gvSIG. En la carpeta::

    src/main/resources-plugin/
    
En contraremos el fichero *"config.xml"*. Contendra:


.. code-block:: xml

    <?xml version="1.0" encoding="ISO-8859-1"?>
    <plugin-config>
        <depends plugin-name="org.gvsig.app.mainplugin" />
        <resourceBundle name="text"/>
        <libraries library-dir="lib"/>
        <extensions>
            <extension class-name="org.gvsig.landregistryviewer.app.mainplugin.LandRegistryViewerExtension"
                               description=""
                               active="true"
                               priority="1">
    			
                <action 
                    name= "view-show-land-regisytry-information"
                    label="_Show_land_registry_information"
                    tooltip="_Show_land_registry_information"
                    action-command="view-show-land-regisytry-information" 
                    icon="view-show-land-regisytry-information"
                    position="001009000000"
                    accelerator=""
                />
            
                <menu 
                    text="View/_Show_land_registry_information"
                    name="view-show-land-regisytry-information"
                />
    			
                <tool-bar name="LandRegistryViewer" position="1">
                    <action-tool name="view-show-land-regisytry-information"/>
                </tool-bar>
    
            </extension>		
    
            <extension class-name="org.gvsig.landregistryviewer.app.mainplugin.DisableEditingExtension"
                               description=""
                               active="true"
                               priority="1">
    			
                <action 
                    name= "layer-start-editing-customized"
                    label="start_edition" 
                    tooltip="start_edition" 
                    position="600700000" 
                    action-command="layer-start-editing"
                    icon="layer-start-editing"
                    accelerator=""
                />
    
    
            </extension>		
        </extensions>
    </plugin-config>

Vamos a ir viendo parte por parte que tenemos en el. Las primeras lineas dentro del tag *plugin-config*...

.. code-block:: xml

    ...
    <plugin-config>
        <depends plugin-name="org.gvsig.app.mainplugin" />
        <resourceBundle name="text"/>
        <libraries library-dir="lib"/>
        <extensions>
    ...

Se encargan de:

- Tag **depends**. Indica que este plugin depende del plugin *org.gvsig.app.mainplugin*. 
  ¿ Esto que quiere decir ? principalmente dos cosas, 
  por un lado que el nuestro plugin se inicializara siempre despues de 
  *org.gvsig.app.mainplugin*, y que se incluira en el classpath de nuestro
  plugin el classpath que tenga el plugin *org.gvsig.app.mainplugin*.
  Con esto nos aseguramos que desde nuetro plugin tendremos acceso a todos
  los componentes definidos en *org.gvsig.app.mainplugin* y los plugins de
  los que este dependa.
  
  Es importante señalar que podemos indicar que dependemos de mas de un
  plugin, de forma que podamos tener acceso no solo a los componentes de
  *org.gvsig.app.mainplugin*, si no ademas, por ejemplo, a los del layout.
  Para esto simplemente repetiremos este tag indicando en cada uno un plugin
  del que dependamos.

- Tag **resourceBundle**. Indica donde ir a buscar los recurso de internacionalizacion de nuestro
  plugin.
  
- Tag **libraries**. Indica donde ha de ir a buscar los jars que tenga nuestro plugin.
  Normalmente el classpath de nuestro plugin estara compuesto, por la ruta a la carpeta de nuestro
  plugin, y la ruta indicada en este tag.

Lo siguiente que encontraremos es el tag **extensions**. Un plugin contiene extensiones... asociadas cada una
de ellas a una clase que implementaremos en nuestro codigo. En esta seccion del *config.xml*,
se definen que estensiones se van a cargar de nuestro plugin. Asi vemos que se definen dos extensiones
que se corresponderan con las clases *LandRegistryViewerExtension* y *DisableEditingExtension*.

En cada una de estas extensiones se definiran todas las acciones que precisemos y que luego se asociaran a menus o botones.
Por ejemplo, en *LandRegistryViewerExtension* se define la accion *view-show-land-regisytry-information*:


.. code-block:: xml

    ...
                <action 
                    name= "view-show-land-regisytry-information"
                    label="_Show_land_registry_information"
                    tooltip="_Show_land_registry_information"
                    action-command="view-show-land-regisytry-information" 
                    icon="view-show-land-regisytry-information"
                    position="001009000000"
                    accelerator=""
                />
    ...


Que luego es usada para definir una entrada de menu y un boton en la barra de botones de gvSIG:

.. code-block:: xml

    ...
                <menu 
                    text="View/_Show_land_registry_information"
                    name="view-show-land-regisytry-information"
                />
    			
                <tool-bar name="LandRegistryViewer" position="1">
                    <action-tool name="view-show-land-regisytry-information"/>
                </tool-bar>
    ...
    
Vamos ahora a ver el código de nuestro *plugin*. Veremos que existen 
tres clases:

- *LandRegistryViewerExtension*.
- *PropertiesOfBlockListener*. 
- *DisableEditingExtension*.


Vamos a ir viendo el codigo de estas tres clases.

La clase *LandRegistryViewerExtension* es la encargada de gestionar la integración del 
codigo que hemos visto en las librerias de la logica y el interface de usuario con gvSIG.
Es la encargada de gestionar las herramientas que nuestro plugin añade a gvSIG en forma
de menus y botones en la barra de herramientas.

Los métodos mas relevantes que vamos a encontrar en la clase *LandRegistryViewerExtension* son:

- **initialize**. Se invoca al cargar la extensión. Aquí nos limitaremos a registrar
  servicios que ofrezca nuestra extensión. En nuestro caso nos limitaremos a registrar en 
  el tema de iconos de la aplicacion las imagenes que vaya a usar nuestro plugin:
  
  .. code-block:: java

     PluginsManager manager = PluginsLocator.getManager();
     IconThemeHelper.registerIcon("action", "view-show-land-regisytry-information", this);

- **postInitialize**. Se invoca durante la inicialización de los plugins, una vez invocado 
  al método *initialize* de todas las extensiones. Esto nos garantiza que cuando se ejecuta
  estarán disponinles prácticamente todos los servicios de gvSIG. Aprovecharemos este método
  para:
    
  - Crear el manager de la parte de lógica de nuestra librería.

    .. code-block:: java

       try {
           manager = LandRegistryViewerLocator.getManager();
  
  - Inicializar los *stores* a través de la función *initializeStores*. 
  
    .. code-block:: java

            this.initializeStores();

  - Y por último crear y mostrar la venta con nuestra vista, a través del método *createViewWindow*. 
    
    .. code-block:: java
    
            viewWindow = this.createViewWindow();

        } catch (LoadLayerException e) {

        }    
    
    Vamos a ver con un poco más de detalle cómo se realiza esto. Antes de empezar 
    lo primero que haremos será obtener una referencia del objeto *aplicacion* y del
    manager de *proyectos*:

    .. code-block:: java

        ApplicationManager application = ApplicationLocator.getManager();

        ProjectManager projectManager = application.getProjectManager();
        
    Ademas de estos dos *managers* obtendremos tambien el *manager* encargado de gestionar la
    internacionalización de la aplicación, que usaremos para traducir algunas de las cadenas
    que vamos a utilizar. 

    .. code-block:: java

       I18nManager i18nManager = ToolsLocator.getI18nManager();
       
       
    Una vez disponemos de estas referencias, podemos crear nuestra vista:
    
    .. code-block:: java

        // 1. Create a new view and set the name.
        ViewManager viewManager = (ViewManager) projectManager.getDocumentManager(ViewManager.TYPENAME);
        ViewDocument view = (ViewDocument) viewManager.createDocument();

        view.setName(i18nManager.getTranslation(MY_VIEW_NAME));
        // Setting view's projection to shapefile's known CRS
        view.getMapContext().setProjection(CRSFactory.getCRS("EPSG:23030"));
        
    Para crear la vista, pediremos al *ProjectManager* que nos devuelva el manager de vistas, y a
    este le pediremos una nueva instancia del documento vista. Debemos recordar aquí, 
    que una de las principales
    funcionalidades de los *manager* es actuar a modo de factorías para obtener instancias de los 
    objetos que gestiona ese *manager*. Una vez disponemos del documento vista, le asignaremos el
    nombre que nosotros consideremos oportuno.
    
    Con la vista ya creada, procederemos a ver cómo hemos de hacer para añadir a ésta las capas que
    necesitemos. Para ello crearemos una capa con las manzamas, esto lo realizaremos a través
    del método *createLayer*, indicándole el nombre de la capa y el *store* en que queremos que
    se base:

    .. code-block:: java
    
        // 2. Create a new layer with the blocks
        FLyrVect layer = (FLyrVect) application.getMapContextManager().createLayer(
                i18nManager.getTranslation("_Blocks"), this.manager.getBlocks());
        
        // Add a new property to the layer to identify.
        layer.setProperty("ViewerLayer", Boolean.TRUE);

    Con la capa ya creada, añadiremos al *mapa* de la vista la nueva capa:

    .. code-block:: java
    
        // 3. Add this layer to the mapcontext of the new view.
        view.getMapContext().getLayers().addLayer(layer);

    Añadiremos al proyecto corriente la vista:

    .. code-block:: java
    
        // 4. Add the view to the current project.
        projectManager.getCurrentProject().add(view);

    Y por último nos encargaremos de presentar la ventana asocida a la vista que
    acabamos de crear:

    .. code-block:: java
    
        // 5. Force to show the view's window.
        IView viewWindow = (IView) viewManager.getMainWindow(view);

        application.getUIManager().addWindow(viewWindow, GridBagConstraints.CENTER);
        try {
            application.getUIManager().setMaximum((IWindow) viewWindow, true);
        } catch (PropertyVetoException e) {
            logger.info("Can't maximize view.", e);
        }

    Una vez ya tenemos mostrada la ventana de la vista, precisaremos registrar en el
    componente gráfico del mapa la nueva herramienta que aportamos:

    .. code-block:: java
    
        // 6. Register my tool in the mapcontrol of the view.
        PropertiesOfBlockListener listener = new PropertiesOfBlockListener();
        viewWindow.getMapControl().addBehavior(TOOL_NAME, new PointBehavior(listener));

  Con todo esto tendremos inicializado nuestro plugin.
  
- *execute*. Este metodo será invocado cada vez que el usuario interactue con las opciones
  de menú o botones que se configuraron en el fichero *config.xml* de nuestro plugin.
  En nuestro caso, se configuró para que se dispare este evento cuando el usuario
  quisiese activar la herramienta de información sobre parcelas catastrales de 
  una manzana. Así que el código que tendríamos que tener ahí debe corresponderse
  con esto:

  .. code-block:: java
  
        if (ACTION_SETINFOTOOL.equalsIgnoreCase(actionCommand)) {
            // Set the tool in the mapcontrol of the active view.
            ApplicationManager application = ApplicationLocator.getManager();
            if (application.getActiveWindow() != viewWindow) {
                return;
            }
            viewWindow.getMapControl().setTool(TOOL_NAME);
        }


  Observaremos que lo primero que hacemos es comprobar si el comando que recibimos
  es el correspondiente a la activación de nuestra herramienta, y que fijamos
  en el *config.xml*. Esto es debido a que en una misma extensión podemos
  agrupar varias herramientas, indicando nombres de comando distintos en el
  *config.xml* para cada una de ellas.
  
  Una vez sabemos que se está tratando de activar nuestra herramienta de información,
  comprobaremos si está activa la ventana de nuestra vista, ya que sobre otras vistas u
  otros tipos de documento, no debemos hacer nada. Y por último, nos dedicaremos
  a activar nuestra herramienta en el mapa de la vista. Herramenta que habíamos registrado
  en el metodo *createViewWindow*.
  

- *isVisible*. En este método deberemos informar si los menús y botones asociados a nuestra
  herramienta deben estar visibles. En nuestro caso dejaremos visible nuestra herramienta
  siempre que la ventana activa sea la de nuestra vista:
  
  .. code-block:: java

    ApplicationManager application = ApplicationLocator.getManager();
    return application.getActiveWindow() == viewWindow;

- *isEnabled*, que devolveremos siempre 'true', ya que nuestra herramienta estará activa 
  siempre que esté visible, y allí ya pusimos las comprobaciones necesarias. Si la 
  logica de nuestra herramienta permite que no esté activa en algunos casos para
  los que sí se permite que esté visible, será aquí donde deberemos realizar esas comprobaciones.


Para terminar vamos a comentar muy de pasada como hemos creado la herramienta sobre la vista.
Una herramienta es la conjuncion de dos componentes, un *Behavior* y un *Listener*. El *Behavior* es el encargado
de interactuar con el usuario para recoger la informacion que se precise de este. En nuestro caso
hemos usado un *PointBehavior*, es decir, este componente se encarga de recoger un *click* del usuario
y una vez recogido le pasa la informacion al *Listener* para que este la procese. En gvSIG podemos encontrarnos 
ya definidos algunos tipos de *Behaviors*, como pueden ser:

- CircleBehavior, captura de usuario la informacion necesaria para definir un circulo.
- MoveBehavior, captura del usuario la informacion para desplazar la vista sobre el mapa.
- PointBehavior, captura un click del usuario
- PolylineBehavior
- PolygonBehavior
- RectangleBehavior
- MouseWheelBehavior

Siendo en general suficientes para la gran mayoria de herramientas que precisemos.
Nosotros deberemos de implementar el *Listener* que se encargue de realizar la operacion que queramos con los datos capturados por el *Behavior*.

Vamos a echarle un vistazo a nuestra clase *Listener*, *PropertiesOfBlockListener*.

  .. code-block:: java
    
    public class PropertiesOfBlockListener extends AbstractPointListener {
    	private static final Logger logger = LoggerFactory.getLogger(PropertiesOfBlockListener.class);
    	
    	private JLandRegistryViewerBlockPanel blockPanel = null;
    	
        public void point(PointEvent event) throws BehaviorException {
            ApplicationManager application = ApplicationLocator.getManager();
            I18nManager i18nManager = ToolsLocator.getI18nManager();
            LandRegistryViewerManager logicManager = LandRegistryViewerLocator.getManager();
            LandRegistryViewerSwingManager guiManager = LandRegistryViewerSwingLocator.getSwingManager();
    
            LandRegistryViewerBlock block;
            try {
                block = logicManager.getBlock(event.getMapPoint());
                if (block == null) {
                    return;
                }
                if( this.blockPanel == null ) {
                	this.blockPanel = guiManager.createJLandRegistryViewerBlockPanel(block);
                	JComponent panel = blockPanel.asJComponent();
    	            ToolsSwingLocator.getWindowManager().showWindow(
    	            		panel, 
    		                i18nManager.getTranslation("_Block_registry_information"),
    		                WindowManager.MODE.TOOL
    		            );
    	            panel.addAncestorListener(new AncestorListener() {
    					public void ancestorRemoved(AncestorEvent arg0) {
    						blockPanel = null; 
    					}
    					public void ancestorMoved(AncestorEvent arg0) {
    					}
    					public void ancestorAdded(AncestorEvent arg0) {
    					}
    				});
                } else {
                	this.blockPanel.setLandRegistryViewerBlock(block);
                }
            } catch (LandRegistryViewerException e) {
            	logger.warn("Can't show block registry information",e);
            	application.message(
            			i18nManager.getTranslation("_Cant_show_block_registry_information"), 
            			JOptionPane.WARNING_MESSAGE
            	);
            }
        }
    
    }
    

Básicamente derivaremos de AbstractPointListener sobre-escribiendo el método point que es llamado para realizar la acción asociada a esta herramienta. Este método se limitará a llamar a nuestro método getBlocks a partir de la posición del mapa sobre la que se hizo clic, y si se obtiene alguna manzana en ese punto se creará nuestro panel para presentar esa información en una ventana.

Dos cosas a destacar:

- Cuando consultemos al parámetro event por las coordenadas del punto sobre el que se ha hecho 
  clic se ha de hacer a través del método *getMapPoint* para asegurarnos que nos lo devuelve en
  coordenadas del mapa y no de pantalla.

- Cuando creemos el panel para presentar la informacion, nos quedaremos escuchando el evento 
  *ancestorRemoved*, para saber cuando el usuario cierre la ventana y asi poder reutilizar la 
  ventana mientras este abierta y saber que hay que abrir una nueva en caso de que el usuario 
  la cierre.
  
