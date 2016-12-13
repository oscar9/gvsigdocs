 
Creación de un nuevo almacén
============================

En otros apartados hemos estado viendo que servicios nos proporciona
DAL para el acceso a los datos tabulares. Normalmente estos servicios
se ofrecen por el :javadoc:`FeatureStore <FeatureStore>`. Sin embargo los servicios relacionados
con la creación física del almacén, no nos los da el :javadoc:`FeatureStore <FeatureStore>`. Estos
servicios forman parte de los servicios que ofrece el :javadoc:`DataServerExplorer <DataServerExplorer>`.

El :javadoc:`DataServerExplorer <DataServerExplorer>` reúne los servicios relacionados con la exploración
de un servidor de datos junto de los de creación de nuevos almacenes en
el servidor. Vamos a ver aquí la parte de añadir un nuevo almacén a un 
servidor de datos, en este caso un sistema de ficheros, el *FilesystemExplorer*.

Un :javadoc:`DataServerExplorer <DataServerExplorer>` dispone para estas tareas de los métodos:

* *getAddParameters*. Este, dado un nombre de almacén registrado, 
  nos devolverá un :javadoc:`DataParameters <DataParameters>` especializado con los parámetros
  que precisa ese el almacén indicado para ser creado.

* *add*, que a partir del :javadoc:`DataParameters <DataParameters>` indicado se encargara de
  crear el nuevo almacén en el servidor.

Veamos un ejemplo de creación de un almacén de tipo *dbf*. Crearemos
un fichero *prueba.dbf* en la carpeta */data*. El fichero *dbf*
contendrá los campos:

* *NOMBRE*, de tipo *STRING* de 100 caracteres.

* *MUNICIPIO*, de tipo *STRING* de 100 caracteres.

* *POBLACION*, de tipo *LONG*.

* *AREA*, de tipo *DOUBLE*.

Para esto tendremos que hacer:

#. Crearemos un :javadoc:`DataServerExplorer <DataServerExplorer>` de tipo *FilesystemExplorer*, indicando
   que el *path* inicial sobre el que ha de trabajar es */data*.

#. Le pediremos al :javadoc:`DataServerExplorer <DataServerExplorer>` una instancia de parámetros adecuada
   para crear un fichero *dbf*.

#. Indicaremos en esos parámetros el nombre del fichero a crear, sin ruta,
   ya que utilizara la ruta corriente del :javadoc:`DataServerExplorer <DataServerExplorer>`  para ello.

#. Le pediremos los parámetros la instancia del :javadoc:`FeatureType <FeatureType>` que se va a 
   utilizar para la creación del *dbf*. Este sera de tipo :javadoc:`EditableFeatureType <EditableFeatureType>`.

#. Añadiremos al :javadoc:`FeatureType <FeatureType>` los atributos que queremos que tenga nuestro
   *dbf*

#. Y por ultimo le indicaremos al :javadoc:`DataServerExplorer <DataServerExplorer>` que añada la nueva
   fuente de datos de la que hemos configurado sus parámetros.

Veamos como queda todo esto junto:

.. code-block:: java

  DataExplorerParameters eparams = manager.createServerExplorerParameters("FilesystemExplorer");
  eparams.setDynValue("initialpath","/data");
  DataServerExplorer serverExplorer = manager.createServerExplorer(eparams);

  NewFeatureStoreParameters sparams = (NewFeatureStoreParameters)serverExplorer.getAddParameters("DBF");
  sparams.setDynValue("dbffilename","prueba.dbf");

  EditableFeatureType featureType = (EditableFeatureType)sparams.getDefaultFeatureType();
  featureType.add("NOMBRE", DataTypes.STRING,100);
  featureType.add("MUNICIPIO", DataTypes.STRING,100);
  featureType.add("POBLACION", DataTypes.LONG);
  featureType.add("AREA", DataTypes.DOUBLE);

  serverExplorer.add(sparams);

En otros apartados hemos estado viendo las operaciones o servicios que ofrece el 
:javadoc:`FeatureType <FeatureType>` de cara a consultar su estructura. Vamos a repasar los servicios que
añade el :javadoc:`EditableFeatureType <EditableFeatureType>` a este de cara a modificar esta:

* *add*. Nos permite añadir nuevos atributos a la descripción del fenómeno.
  Le indicaremos el nombre del nuevo atributo y el tipo y nos devolverá una
  instancia de :javadoc:`EditableFeatureAttributeDescriptor <EditableFeatureAttributeDescriptor>` , que es similar a 
  :javadoc:`FeatureAttributeDescriptor <FeatureAttributeDescriptor>`, pero añadiendo operaciones que permiten
  modificar las propiedades del atributo.

* *getNotEditableCopy*, que nos devuelve una copia de el mismo no editable, 
  es decir un :javadoc:`FeatureType <FeatureType>`.

* *getSource*, nos devuelve una referencia al :javadoc:`FeatureType <FeatureType>` del que creamos
  esta versión editable. Si el :javadoc:`EditableFeatureType <EditableFeatureType>` no se creo a partir de 
  un :javadoc:`EditableFeature <EditableFeature>` devolverá *null*.

* *remove*, que nos permite eliminar una descripción de atributo.

* *setDefaultGeometryAttributeName*, que nos permite indicar cual es el
  nombre del atributo que contiene la geometría a usar como geometría
  por defecto del fenómeno.

Veamos algunos detalles interesantes del método *add*. Este método esta sobrecargado
y presenta tres variantes:

* *add(String name, int type)*. Se trata de la versión simple, y crea, añade y
  devuelbe un nuevo atributo del nombre y tipo indicado.

* *add(String name, int type, int size)*. Se comporta de forma similar al anterior,
  pero permite fijar el tamaño. Es útil con el tipo *STRING* para especificar
  de forma cómoda el tamaño de este.

* *add(String name, int type, Evaluator eval)*. Con este podemos añadir
  nuevos atributos cuyos valores se calculen usando el evaluador indicado.
  En otro apartado se vera esto con mas detalle (`Soporte para atributos calculados`_).


.. _Soporte para atributos calculados : api_soporte_para_atributos_calculados.html
