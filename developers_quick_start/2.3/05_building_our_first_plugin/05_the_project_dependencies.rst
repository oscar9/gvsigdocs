  
  
Las dependencias en los distintos proyectos
-------------------------------------------

A la hora de identificar un *artefacto* en maven, lo normal es que que intervengan al menos tres componentes, sin los cuales no es posible identificar al *artefacto*. Estos serian:

- Grupo, **groupId**. Normalmente hace referencia al nombre de nuestra organización.
  Por ejemplo en gvSIG utilizamos *"org.gvsig"*, por otras organizaciones o empresas pueden
  utilizar uno propio.

- Identificador, **artefactId**. Deber ser un identificador del proyecto único dentro del
  grupo.

- Versión, **version**. En el proceso de desarrollo iremos generando distintas revisiones o versiones
  de nuestros productos o *artefactos*. Para identificarlos no solo deberemos indicar su nombre, 
  si no también la versión a la que nos referimos.

Todo proyecto maven deberá identificarse por estos tres datos. Así en el *pom.xml* del raíz de nuestro
proyecto encontraremos algo como:

.. code-block:: xml

   <groupId>org.gvsig</groupId>
   <artifactId>org.gvsig.landregistryviewer</artifactId>
   <version>1.0.0-SNAPSHOT</version>

Donde se declaran esos valores.

Además en el *pom* de nuestro proyecto raíz también encontraremos:

- Una declaración de quien es el padre o de quien extiende nuestro proyecto, sección **parent**:

  .. code-block:: xml
    
    <parent>
      <groupId>org.gvsig</groupId>
      <artifactId>org.gvsig.desktop</artifactId>
      <version>2.0.157</version>
    </parent>
    

  Aquí observaremos que, normalmente, en un proyecto gvSIG, extenderemos o heredaremos del proyecto *org.gvsig:org.gvsig.desktop*.
  Este proyecto tiene la configuración necesaria para compilar y desplegar proyectos gvSIG, normalmente plugins para la aplicacion
  *gvSIG desktop* y las librerias que estos precisen. Además de la configuración básica, también tiene declarada la versión de
  todos los componentes que forman el núcleo de gvSIG, de forma que no tengamos que declararla cada vez que indiquemos una dependencia 
  con un subproyecto del núcleo.
  

- Una sección donde declararemos las versiones de los subproyectos que conforman nuestro
  proyecto. En maven esto lo realizaremos en la sección **dependencyManagement**. Normalmente aquí
  especificaremos:
  
  - Todos los subproyectos de nuestro proyecto.
  
  - Las dependencias con los proyecto o artefactos que no formen parte del 
    núcleo de gvSIG, como otras librerías que podamos necesitar.
  
  Hay que tener en cuenta que en esta sección, **dependencyManagement**, indicamos que versiones de que artefactos queremos usar cuando especifiquemos una dependencia con estos en la sección **dependencies**. Que aparezca una depedencia en  **dependencyManagement** no le esta indicando a *maven* que exista una dependencia real con ese artefacto, esto solo sucederá con las que se incluyan en la sección **dependencies**.

  Para nuestro caso tendríamos algo como:
  
  .. code-block:: xml

    <dependencyManagement>
      <dependencies>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.lib.api</artifactId>
          <version>1.0.0-SNAPSHOT</version>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.lib.api</artifactId>
          <version>1.0.0-SNAPSHOT</version>
          <type>test-jar</type>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.lib.impl</artifactId>
          <version>1.0.0-SNAPSHOT</version>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.swing.api</artifactId>
          <version>1.0.0-SNAPSHOT</version>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.swing.api</artifactId>
          <version>1.0.0-SNAPSHOT</version>
          <type>test-jar</type>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.swing.impl</artifactId>
          <version>1.0.0-SNAPSHOT</version>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.app.mainplugin</artifactId>
          <version>1.0.0-SNAPSHOT</version>
        </dependency>
      </dependencies>
    </dependencyManagement>
    

  Muy importante que las versiones de los subproyectos aquí indicadas coincidan siempre con la versión del proyecto raíz.

  La finalidad principal sera centralizar en este *pom* la declaración de las versiones de las
  dependencias de nuestro proyecto de cara a facilitar su mantenimiento.
  
  Si dependediesemos de algún otro proyecto de gvSIG, normalmente lo que haremos seria importar las 
  dependencias que este tuviese declaradas en su *dependencyManagement* en lugar de ir declarando una
  a una las que precisemos. Esto reduce la posibilidad de confusión al declarar las dependencias
  de estos. Así por ejemplo si quisiésemos declarar las versiones de los subproyectos base de raster
  pondríamos algo como:
  
  .. code-block:: xml
    
    <dependencyManagement>
      <dependencies>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.raster</artifactId>
          <version>${org.gvsig.raster.version}</version>
          <type>pom</type>
          <scope>import</scope>
        </dependency>
        <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.lib.api</artifactId>
          <version>1.0.0-SNAPSHOT</version>
        </dependency>
    ...
  
  
  Y declararíamos la propiedad *"org.gvsig.raster.version"* en la sección *properties*:

  .. code-block:: xml
  
    <properties>
      <org.gvsig.raster.version>2.2.52</org.gvsig.raster.version>
    </properties>
    

  Podemos o no usar un *property* para indicar la versión del proyecto del cual importamos las dependencias.
  La ventaja de usar un *property* es que podemos actualizar nuestro proyecto fácilmente a la ultima revisión
  de gvSIG con::
  
    mvn versions:update-parent  versions:update-properties
    
  Pero antes de ejecutar esto es conveniente informarse de si se ha generado ya alguna versión mas nueva que con
  la que estamos trabajando, en nuestro caso 2.3.1-2501.
  
Una cuestión mas a tener en cuenta relacionada con las versiones de los artefactos que podemos encontrar en 
el *"pom"* es que estas no se corresponden con las versiones de la aplicación "gvSIG desktop". La aplicación
de usuario lleva su linea de versiones y cada uno de los artefactos de gvSIG la suya. Siendo así...

¿ Como podemos saber que versión de artefacto debemos usar ?

Para saber la versión de *org.gvsig.desktop* que debemos usar, deberemos decidir contra que versión de
la aplicación *gvSIG desktop* deberemos trabajar, y disponer de una versión de esta instalada.

Miraremos la versión del jar::

  org.gvsig.andami-*.jar

de dentro de la carpeta *"lib"* de la instalación de gvSIG. La versión de ese jar nos dirá la versión
que debemos usar como versión de *"org.gvsig.desktop"*.

Para *"gvSIG desktop 2.3.1-2501"* es **2.0.157**.

En el caso de la librería base de raster, org.gvsig.raster, en la carpeta
*lib* del plugin *org.gvsig.raster.mainplugin*, podemos encontrar::

  org.gvsig.raster.lib.api-*.jar

del que podemos obtener la versión de esta. O si queremos enlazar con el plugin de *"mapas"*, consultaríamos
en el plugin *org.gvsig.app.document.layout2.app.mainplugin*, en la carpeta *lib* la versión de::

  org.gvsig.app.document.layout2.app.mainplugin-*.jar

Las versiones de las dependencias mas comunes a usar podemos encontrarlas en:

.. list-table:: Proyectos mas comunes
   :header-rows: 1

   * - Proyecto
     - jar
     
   * - org.gvsig.desktop
     - lib/org.gvsig.andami-...jar

   * - org.gvsig.raster
     - gvSIG/extensiones/org.gvsig.raster.mainplugin/lib/org.gvsig.raster.lib.api-...jar
     
   * - org.gvsig.geoprocess
     - gvSIG/extensiones/org.gvsig.geoprocess.app.mainplugin/lib/org.gvsig.geoprocess.app.mainplugin-...jar
     
   * - org.gvsig.hyperlink.app
     - gvSIG/extensiones/org.gvsig.hyperlink.app.extension/lib/org.gvsig.hyperlink.app.extension-...jar
     
   * - org.gvsig.mapsheets.app
     - gvSIG/extensiones/org.gvsig.mapsheets.app.mainplugin/lib/org.gvsig.mapsheets.app.mainplugin-...jar
     
   * - org.gvsig.vectorediting
     - gvSIG/extensiones/org.gvsig.vectorediting.app.mainplugin/lib/org.gvsig.vectorediting.app.mainplugin-...jar
     
   * - org.gvsig.tools
     - lib/org.gvsig.tools.lib-...jar

Además de la sección **parent** y **dependencyManagement**, en el *pom* podemos encontrarnos una sección **dependencies**.
Esta sección es donde se declaran las dependencias reales de nuestro proyecto, a partir de las que se construirá el 
*classpath* de compilación y ejecución. Sin embargo recomendamos que en proyectos de tipo *pom*, como es el raíz, 
no incluyamos ninguna dependencia, incluyéndolas únicamente en los proyectos *hoja*, de tipo *jar*, ya que 
en un momento dado puede desvirtuarnos los informes de dependencias de nuestros proyectos.

Por ultimo, relacionado con el *pom* del proyecto raíz, es importante la sección **modules**, que indica cuales son
los subproyectos de este. Es importante tener en cuenta que una carpeta dentro del proyecto raíz no es un subproyecto 
o modulo de este si no se declara aquí.


Vamos a comentar ahora sobre los demás *poms* que encontraremos en los distintos subproyectos.

Primero, sobre los *poms* de los subproyectos o módulos de tipo *pom*, que no son subproyectos *hoja*.
En estos lo mas importante es tener en cuenta que:

- Deberemos indicar siempre una sección **parent** en la que referenciaremos al pom padre
  de este, utilizando su **groupId**, **artifactId** y **version**.
  
- Incluiremos solo **artifactId**, ya que la *version* y *groupId* lo heredara del padre.

- No incluiremos sección **dependencies**.

- Incluiremos la sección **modules** con los subproyectos de este.


Ahora pasemos a ver que tendremos en los *poms* de los proyectos *hoja*, de tipo *jar*.
Estos proyectos son los que generan *artefactos*, normalmente *jars* con nuestro código y
los que precisan que especifiquemos dependencias de cara a su correcta compilación. Asi 
que sera en estos donde deberemos incluir la sección **dependencies**. A la hora de 
especificar una dependencia tendremos que tener en cuenta que:

- Siempre especificaremos su **groupId** y **ArtefactId**.

- No especificaremos nunca su **version**, esta deberemos haberla incluido
  en el **dependenciesManagement** de nuestro proyecto raíz o vendrá heredada
  del proyecto *org.gvsig.desktop*.
  
- Deberemos especificar siempre el *scope* de la dependencia. Aunque maven no 
  lo requiere siendo *compile* si no se especifica, desde el proyecto gvSIG 
  recomendamos que se incluya siempre de cara a evitar confusiones.

  Normalmente el **scope** sera:
  
  - compile
  - runtime
  
  Aunque hay algunos mas. Este nos indicara si esa dependencia debe incluirse en 
  el *classpath* de compilación o de ejecución.
  
  
Vamos a comentar ahora algunas consideraciones sobre las dependencias entre los
distintos subproyectos. En gvSIG tendemos a separar *conceptos*, API/implementacion por un lado y 
logica/GUI/plugin, influyendo esto directamente en la estructura de proyectos, ya que
como vimos anteriormente disponemos de un proyectos para la lógica (...lib) con su 
API e implementación, otro proyecto para el interface de usuario (...swing), también
con su API e implementación, y otro para los plugins de gvSIG (...app). 
Ahora bien... ¿ Que dependencias podemos encontrar en cada uno de ellos ?

Normalmente tendremos en cuenta una serie de reglas:

- La definición de APIs solo dependerá de proyectos que definan APIs.
  Normalmente en los proyectos que definen APIs su *artifactId* termina en *".api"*.
  
- Nadie tendrá dependencias con proyectos que contengan la implementación de un API, 
  dependiendo estos exclusivamente de APIs.
  
- En general, una librería, de API o implementación, nunca dependerá de una implementación, 
  un plugin de gvSIG a el framework de runtime de la aplicación gvSIG (andami).

- Las librerías que contienen el interface de usuario, tanto su API como su implementación,
  no deberán depender de la librería que implementa la lógica de ese interface de usuario,
  solo de su API.

- Solo los proyectos de tipo plugin de gvSIG podrán tener dependencias con otros plugins
  o el framework de ejecución de gvSIG.
  
Con esto en mente podemos echar un vistazo a las dependencias de los distintos *poms*
de nuestros proyectos.

- **org.gvsig.landregistryviewer.lib.api**:

  .. code-block:: xml

    <dependencies>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.tools.lib</artifactId>
        <scope>compile</scope>
      </dependency>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.tools.lib</artifactId>
        <type>test-jar</type>
        <scope>test</scope>
      </dependency>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.fmap.geometry.api</artifactId>
        <scope>compile</scope>
      </dependency>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.fmap.dal.api</artifactId>
        <scope>compile</scope>
      </dependency>
    </dependencies>

  Depende de la librería *org.gvsig.tools.lib*, que contiene todas las utilidades básicas
  para separación de API e implementación, con lo que prácticamente todos nuestros proyectos
  dependerán de ella, y del API de las librerías de acceso a datos, *org.gvsig.fmap.dal.api*
  y de manejo de geometrías, *org.gvsig.fmap.geometry.api*. Como se aprecia, solo depende
  de APIs.

- **org.gvsig.landregistryviewer.lib.impl**, básicamente mantiene las mismas dependencias
  que el API, pero además tiene dependencias de compilación con este.
  
- **org.gvsig.landregistryviewer.swing.api**:

  .. code-block:: xml

    <dependencies>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.tools.lib</artifactId>
        <scope>compile</scope>
      </dependency>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.tools.lib</artifactId>
        <type>test-jar</type>
        <scope>test</scope>
      </dependency>
      <dependency>
        <groupId>org.gvsig</groupId>
        <artifactId>org.gvsig.tools.swing.api</artifactId>
        <scope>compile</scope>
      </dependency>
      <dependency>
          <groupId>org.gvsig</groupId>
          <artifactId>org.gvsig.landregistryviewer.lib.api</artifactId>
          <scope>compile</scope>
      </dependency>
    </dependencies>    


  Dependerá del API de la parte de lógica, *org.gvsig.landregistryviewer.lib.api*, y además
  del API de la parte de *swing* de la libreria org.gvsig.tools, *org.gvsig.tools.swing.api*.

- **org.gvsig.landregistryviewer.swing.impl**, dependerá básicamente de su API.

- **org.gvsig.landregistryviewer.app.mainplugin**, la parte del plugin para gvSIG desktop, 
  dependerá de:

  .. code-block:: xml  
  
    <dependencies>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.landregistryviewer.lib.api</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.landregistryviewer.swing.api</artifactId>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.tools.lib</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.tools.swing.api</artifactId>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.andami</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.app.mainplugin</artifactId>
            <scope>compile</scope>
        </dependency>
        
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.fmap.control</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.fmap.mapcontext.api</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.fmap.geometry.api</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.fmap.dal.api</artifactId>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.projection.api</artifactId>
            <scope>compile</scope>
        </dependency>

        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.landregistryviewer.lib.impl</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.gvsig</groupId>
            <artifactId>org.gvsig.landregistryviewer.swing.impl</artifactId>
            <scope>runtime</scope>
        </dependency>
                
    </dependencies>
    

  
  Como se trata del plugin para gvSIG, este depende específicamente del framework
  de la aplicación gvSIG desktop, *org.gvsig.andami*, y del plugin principal de la aplicación, 
  *org.gvsig.app.mainplugin*. Además depende de algunas otras librerías de gvSIG, como:
  
  - org.gvsig.fmap.control, componente gráfico para visualizar un mapa.
  - org.gvsig.fmap.mapcontext.api, la parte de lógica del componente del mapa.
  - org.gvsig.fmap.geometry.api, la librería de geometrías.
  - org.gvsig.fmap.dal.api, la librería de acceso a datos.
  - org.gvsig.projection.api, la librería de proyecciones.
  - org.gvsig.tools.lib.
  - org.gvsig.tools.swing.api.

  Además de depender para compilación de los APIs de las librerías del proyecto:
  
  - org.gvsig.landregistryviewer.lib.api
  - org.gvsig.landregistryviewer.swing.api
  
  Y lo mas importante, deberá depender de las implementaciones de las librería del 
  proyecto en modo runtime.

  - org.gvsig.landregistryviewer.lib.impl
  - org.gvsig.landregistryviewer.swing.impl
  
  Es importante esto por dos razones. Por un lado deben ser solo de runtime, si no significaría
  que no hemos definido correctamente el API de nuestro proyecto, y por otro, es preciso que 
  se incluya la dependencia como de runtime ya que necesitaremos desplegarlas en nuestro 
  plugin y para ello es obligatorio que nuestro plugin dependa de ellas, como veremos mas
  adelante cuando comentemos el código del plugin.
    
