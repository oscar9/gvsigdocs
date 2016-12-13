 
Soporte de índices
==================

En esta sección se dará una breve introducción a los siguientes conceptos
u operaciones sobre un almacén de datos:

* Alcance del soporte de índices
* Crear un índice sobre un almacén
* Obtener los índices de un almacén
* Obtener un índice por nombre
* Consultar un índice
  
  * Coincidencia (match)
  * Rango
  * N elementos más cercanos
  * N elementos más cercanos con tolerancia

* El índice

  * Obtener su tipo de dato
  * Obtener su nombre
  * Obtener los atributos asociados
  * Insertar un elemento
  * Eliminar un elemento

* Algunas recomendaciones sobre el uso de índices



Alcance del soporte de índices
------------------------------

El soporte de índices que ofrece DAL actualmente consiste en la creación y manejo de índices temporales y locales a nivel de aplicación. El formato y ubicación real en que estos índices se almacenan depende de la librería que provee la implementación y no forma parte de las características del API. 

Una vez se ha creado un índice sobre un almacén, éste lo utilizará para optimizar las consultas de forma automática cuando considere oportuno. A pesar de que este es el uso común, también se puede acceder a un índice manualmente y manipular su contenido. 


Crear un índice sobre un almacén
--------------------------------

Para crear un índice sobre un almacén de fenómenos, se utiliza el método createIndex(...) de la interfaz :javadoc:`FeatureStore <FeatureStore>` . Este método recibe como parámetros el tipo de fenómeno :javadoc:`FeatureType <FeatureType>` y el nombre del atributo (que debe pertenecer al tipo de fenómeno especificado) sobre el que se desea crear un índice. Así mismo es necesario indicar un nombre para el índice, que servirá para identificarlo.

.. code-block:: java
		  
   DataManager manager;
   DataStoreParameters params;
   FeatureStore store;
   manager = DALLocator.getDataManager();
		  
   params = manager.createStoreParameters("Shape");
   params.setAttribute("shpfileName","data/prueba.shp");
   store = (FeatureStore) manager.createStore(params);
   FeatureType fType = store.getDefaultFeatureType();
   store.createIndex(fType, "municipio", "municipios_idx");
   store.dispose();


La creación de un índice puede ser una operación costosa, por lo que el método se ha sobrecargado para admitir un observador, de manera que el proceso se pueda lanzar en segundo plano, y el observador sea notificado del progreso del mismo.

.. code-block:: java
		  
   (...)
   store.createIndex(fType, "municipio", "municipios_idx", this);
   (...)

Obtener los índices de un almacén
---------------------------------

Los índices de un almacén están almacenados en un :javadoc:`FeatureIndexes <FeatureIndex>` . Se obtiene a partir del almacén y se puede iterar sobre él para acceder a todos los índices disponibles:

.. code-block:: java

   (...)
   FeatureIndexes indexes = store.getIndexes();
   Iterator it = indexes.iterator();			  
   while (it.hasNext()) {
      FeatureIndex index = (FeatureIndex) it.next();
      System.out.println(index.getName());
   }
   (...)


Obtener un índice por nombre
----------------------------

El siguiente ejemplo muestra como obtener un índice del que se conoce el nombre:


.. code-block:: java

   (...)
   FeatureIndex index = store.getIndexes().getFeatureIndex("municipio_idx");
   System.out.println(index.getDataType());
   System.out.println(index.getAttributeNames());
   (...)

Como se observa en el ejemplo, el índice dispone de un método *getAttributeNames* que devuelve una *List* de *String* que contienen los nombres de los atributos indexados. En la actualidad sólo se soporta la creación de índices sobre un sólo atributo (en el método *createIndex* sólo se puede especificar un atributo), sin embargo el índice está preparado por si en el futuro se soporta la creación de índices multi-atributo. 

Consultar un índice
-------------------

Hay varias modalidades de consulta de un índice. 

Coincidencia (match)
....................

Este tipo de consulta devuelve los elementos del índice que intersectan con el conjunto definido por el objeto que se pasa como parámetro. Es decir, si se pasa un número entero y el índice es de tipo entero, entonces devolverá los elementos que sean iguales al número entero. Si por el contrario el parámetro representa un intervalo numérico entonces la consulta devolverá todos los elementos que pertenecen al intervalo. Si el índice es espacial y el parámetro es una geometría entonces la consulta devuelve los elementos que intersectan con ella. En el caso de datos espaciales, hay que tener en cuenta que los índices clasifican los *extent* de las geometrías con una precisión relativa a la granularidad (o profundidad) del índice por lo que se da el caso de que en el resultado hay geometrías que no intersectan realmente con la geometría parámetro, sino que lo que se devuelve es una aproximación que siempre es un superconjunto del resultado exacto.

El siguiente ejemplo hace una consulta espacial de tipo *match* sobre un índice:
   
.. code-block:: java

   (...)
   GeometryManager gf = GeometryLocator.getGeometryManager();
   Geometry geom = gf.createSurface(new GeneralPathX(
       new Rectangle(1,1,100,100)),
       Geometry.SUBTYPES.GEOM2D);
   FeatureSet set = index.getMatchFeatureSet(geom.getEnvelope());
   Iterator it = set.iterator();
   while (it.hasNext()) {
      Feature feat = (Feature) it.next();
      System.out.println(feat.toString());
   }
   (...)


Rango
.....

Este tipo de consulta recibe dos parámetros que delimitan un rango o intervalo. La consulta devuelve los elementos que pertenecen a este intervalo. Es un tipo de consulta pensado para atributos alfanuméricos o tipos de datos con una relación de orden, ya que en el caso de los datos espaciales no existe esta relación de orden y para filtrar por un rango se usa el tipo *match* y el rango viene representado por una geometría.

.. code-block:: java

  (...)
  FeatureSet set = index.getRangeFeatureSet(value1, value2);
  (...)


N elementos más cercanos
........................

Este tipo de consulta devuelve los n elementos más cercanos al valor de referencia que se pasa como parámetro.  

.. code-block:: java

  (...)
  FeatureSet set = index.getNearestFeatureSet(count, value);
  (...)

N elementos más cercanos con tolerancia
.......................................

Este tipo de consulta funciona igual que la anterior pero se especifica una distancia máxima permitida del valor de referencia. Esta distancia máxima, o tolerancia, sirve para obtener N elementos como máximo, estando todos ellos a una distancia del valor de referencia menor o igual que la tolerancia indicada.

.. code-block:: java

  (...)
  FeatureSet set = index.getNearestFeatureSet(count, value, tolerance);
  (...)


El índice
---------

El índice dispone de un conjunto de servicios para obtener información sobre él así como para manipular su contenido directamente. Son los siguientes:

Obtener su tipo de dato
.......................

El tipo de dato viene expresado por una de las constantes definidas en :javadoc:`DataTypes <DataTypes>`

.. code-block:: java

	/** Data type */
	public int getDataType();

Obtener su nombre
.................

El nombre identifica al índice dentro de un almacén.

.. code-block:: java

	/** Index name */
	public String getName();
	
Obtener los atributos asociados
...............................

La lista de nombres de los atributos indexados en el índice. En principio siempre tendrá un sólo elemento ya que de momento sólo se soporta la indexación por un único atributo. El servicio se incluye en previsión de que en el futuro se de soporte a índices compuestos. 

.. code-block:: java

	/** Attribute names */
	public List getAttributeNames();


Insertar un elemento
....................

Permite insertar un fenómeno o un conjunto de fenómenos en el índice. 

.. code-block:: java

	/**
	 * Inserts a Feature in the index.
	 * The Feature must contain a column that matches this index's column (name and data type)
	 * @param feat
	 */
	public void insert(Feature feat);

	/**
	 * Inserts a FeatureSet into this index
	 * FeatureType is not checked so it will accept any FeatureType
	 * as long as exists a column with a valid name
	 */	
	public void insert(FeatureSet data) throws DataException;
	


Eliminar un elemento
....................

Permite eliminar un fenómeno o un conjunto de fenómenos del índice.

.. code-block:: java

	/**
	 * Deletes a Feature in the index.
	 * The Feature must contain a column that matches this index's column (name and data type)
	 * @param feat
	 */
	public void delete(Feature feat);

	/**
	 * Deletes a FeatureSet from this index
	 * FeatureType is not checked so it will accept any FeatureType
	 * as long as exists a column with a valid name
	 */	
	public void delete(FeatureSet data) throws FeatureIndexException;


Algunas recomendaciones sobre el uso de índices
-----------------------------------------------

El uso normal de los índices consiste únicamente en crearlos, porque es el propio almacén quien decide cuándo utilizarlos. Aun así es posible crear un índice y añadir o eliminar elementos al mismo. Esta posibilidad se ha dejado abierta para casos especiales en que se quiera tener control sobre el índice.

Se recomienda no crear índices locales para bases de datos que soporten índices. Es mucho mejor crear el índice en la base de datos (claro que normalmente hay que ser administrador de la misma) y así que sea la propia base de datos quien decida cuándo usarlo.

En cuanto a la creación de los índices, debido al coste de esta operación normalmente es recomendable ejecutar el proceso de indexación en segundo plano.    
