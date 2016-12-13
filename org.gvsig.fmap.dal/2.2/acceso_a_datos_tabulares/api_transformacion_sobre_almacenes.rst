 
Transformaciones sobre almacenes
================================

Introducción
============

Una transformación es un algoritmo que a partir de un fenómeno de un tipo produce otro fenómeno de otro tipo distinto, sin modificar el original. En otras palabras, copia una :javadoc:`Feature <Feature>` cuyo :javadoc:`FeatureType <FeatureType>` es A en otra :javadoc:`Feature <Feature>` cuyo :javadoc:`FeatureType <FeatureType>` es B, siendo la transformación *T* una función de correspondencia entre los :javadoc:`FeatureType <FeatureType>` *A* y *B*: *T(A)=B*.

Este mecanismo se ha ideado para dar la posibilidad de crear vistas sobre almacenes de datos sin que sea necesario modificar los mismos, para que los datos se presenten al usuario de forma diferente a como están realmente estructurados.

DAL ofrece una interfaz para poder añadir transformaciones a un almacén. Esta interfaz se llama :javadoc:`FeatureStoreTransform <FeatureStoreTransform>`.


La interfaz FeatureStoreTransform
=================================

Esta interfaz representa a una transformación y permite:

* Obtener el tipo de fenómeno por defecto de la transformación así como la lista completa de sus tipos de fenómeno si tiene más de uno.

* Obtener y fijar el almacén que es objeto de la transformación.

* aplicar la transformación a un fenómeno.

.. code-block:: java

  public interface FeatureStoreTransform extends Persistent {

    public FeatureType getDefaultFeatureType() throws DataException;

    public List getFeatureTypes() throws DataException;

    public FeatureType getSourceFeatureTypeFrom(FeatureType targetFeatureType);

    public void applyTransform(Feature source, EditableFeature target)
            throws DataException;

    public void setFeatureStore(FeatureStore featureStore);

    public FeatureStore getFeatureStore();

    public boolean isTransformsOriginalValues();
  }

Obtener las transformaciones de un almacén
==========================================

Las transformaciones de un almacén se guardan en un contenedor de transformaciones de tipo :javadoc:`FeatureStoreTransforms <FeatureStoreTransforms>`. Se obtiene así:

.. code-block:: java

  FeatureStore store = (...);

  FeatureStoreTransforms transforms = store.getTransforms();


Añadir una transformación a un almacén
======================================

Las transformaciones se añaden a través del :javadoc:`FeatureStoreTransforms <FeatureStoreTransforms>` asociado al almacén:

.. code-block:: java

  FeatureStore store = (...);
  FeatureStoreTransform transform = (...);
  
  (...)
  
  store.getTransforms().add(transform);



Ejemplo de implementación de una transformación
===============================================

Para facilitar la implementación de transformaciones, existe una clase abstracta que aporta una implementación de
:javadoc:`FeatureStoreTransform <FeatureStoreTransform>` y se recomienda extender. Esta clase se llama :javadoc:`AbstractFeatureStoreTransform <AbstractFeatureStoreTransform>`.  


Al implementar una transformación, hay que tener en cuenta tres aspectos  que no son evidentes porque no forman parte de la interfaz:

#. *Constructor vacío*: Al tratarse de una entidad persistente requiere un constructor sin parámetros para poder instanciarse cuando aún no se dispone de los datos persistidos.
#. *Implementar los métodos de Persistent_*: para que el almacén pueda almacenarse y recuperar el estado en el que se persistió, las transformaciones deben ser capaces de persistir y recargarse.
#. *Método de inicialización*: Por el mismo motivo, conviene realizar la inicialización en un método aparte del constructor (por ejemplo *initialize(...)*). De esta forma quien utilice la transformación llamará al método de inicialización pasándole la información necesaria, una vez se disponga de ella.

Añadir un atributo calculado
----------------------------

El siguiente ejemplo muestra cómo se implementa una sencilla transformación. Esta transformación añade un attributo geométrico al :javadoc:`FeatureType <FeatureType>` a partir de dos atributos de tipo numérico que contienen las *X* y las *Y*:  

.. code-block:: java

  /**
    * 
    * This transform adds a new attribute of type Geometry to the
    * original store's default FeatureType. When applying the transform 
    * to a single feature this new attribute is assigned the value of a 
    * point whose coordinates proceed from two numeric attributes from the
    * store, called xname, yname.
    *
    */
  public class MyTransform extends AbstractFeatureStoreTransform {

    private String geomName;
	private String xname;
	private String yname;

    private GeometryManager geomManager;

	/**
	 * A default constructor with out parameters
	 */
	public MyTransform() {
		this.geomManager = GeometryLocator.getGeometryManager();

	}

	/**
	 * Initializes the transform by assigning the source store and the names of
	 * the necessary attributes.
	 *
	 * @param store
	 *            source store.
	 *
	 * @param geomName
	 *            name of the geometry attribute in the default feature type
	 *            from the source store.
	 *
	 * @param xname
	 *            name of the attribute containing the X coordinates
	 *
	 * @param yname
	 *            name of the attribute containing the Y coordinates
	 *
	 * @throws DataException
	 */
	public MyTransform initialize(FeatureStore store, String geomName,
			String xname, String yname) throws DataException {

		// Initialize some data
		setFeatureStore(store);
		this.geomName = geomName;
		this.xname = xname;
		this.yname = yname;

		// obtain the feature type, add the new attribute
		// and keep a reference to the
		// resulting feature type
		EditableFeatureType type = getFeatureStore().getDefaultFeatureType()
				.getEditable();
		type.add(geomName, DataTypes.GEOMETRY);
		List list = new ArrayList(1);
		list.add(type.getNotEditableCopy());
		setFeatureTypes(list, (FeatureType) list.get(0));
		return this;
	}

	/**
	 * Applies this transform to a target editable feature, using data from the
	 * source feature.
	 */
	public void applyTransform(Feature source, EditableFeature target)
			throws DataException {

		// copy source feature data over target feature
		target.copyFrom(source);

		// calculate and assign new attribute's value
		Geometry geom;
		try {
			geom = geomManager.createPoint(source.getDouble(xname), source
					.getDouble(yname), Geometry.SUBTYPES.GEOM2D);
		} catch (CreateGeometryException e) {
			throw new ReadException("XYTranform", e);
		}
		target.setGeometry(this.geomName, geom);
	}


    public void saveToState(PersistentState state) throws PersistenceException {
		state.set("xname", xname);
		state.set("yname", yname);
		state.set("geomName", geomName);
	}

    public void loadFromState(PersistentState state)
			throws PersistenceException {
		this.xname = state.getString("xname");
		this.yname = state.getString("yname");
		this.geomName = state.getString("geomName");
	}

    /**
     * Returns the FeatureType to use to get data from original store
     */
	public FeatureType getSourceFeatureTypeFrom(FeatureType targetFeatureType) {
		EditableFeatureType ed = targetFeatureType.getEditable();
		ed.remove(this.geomName);
		return ed.getNotEditableCopy();
	}

	/**
	 * Informs that original values of store don't will be modified
	 */
	public boolean isTransformsOriginalValues() {
		return false;
	}

  }


Y para aplicar la transformación haríamos algo como:

.. code-block:: java

  FeatureStoreTransform transform = new MyTransform().initialize(store,"geom","x","y");
  store.getTransforms().add(transform);


JOIN de dos almacenes
---------------------

El siguiente ejemplo realiza una operación *JOIN* parcial sobre dos tipos de fenómeno de dos almacenes. Siendo *A* el conjunto de atributos de un tipo de fenómeno y *B* el conjunto de atributos de otro tipo de fenómeno, definimos nuestra transformación como la unión de *A* con un subconjunto de *B*. El subconjunto de *B* se especifica como parámetro con un array con el nombre de los atributos que se desea incluir en la unión. 

Además de la transformación del tipo de fenómeno, al que se le añaden nuevos atributos, la unión de los datos de los dos almacenes se realiza utilizando el atributo especificado como clave ajena, de forma que para cada fenómeno del almacén principal se busca un fenómeno en el segundo almacén tal que  keyAttr1 == keyAttr2.    


Si existen atributos que se llaman igual en ambos tipos de fenómeno, en la unión resultante se renombrará los de *B*. 

.. code-block:: java

 public class JoinTransform extends AbstractFeatureStoreTransform {
    
	/**
	 * Store from which the join transform will get the additional attributes
	 */
	private FeatureStore store2;

	/**
	 * name of the key attr in store1 that will be used to match features in
	 * store2
	 */
	private String keyAttr1;

	/**
	 * name of the key attr in store2 that will be used to match features in
	 * store1
	 */
	private String keyAttr2;

	/**
	 * names of the attributes to join from store2 to store1
	 */
	private String[] attrs;

	/**
	 * Attribute names may change after transformation if they are repeated in
	 * both stores. This map keeps correspondence between store2 original names
	 * and their transformed counterparts.
	 */
	private Map targetNamesMap;

	private JoinTransformEvaluator evaluator = null;

	private FeatureType originalFeatureType = null;

	private String[] attrsForQuery;

	/**
	 * A default constructor
	 */
	public JoinTransform() {
		targetNamesMap = new HashMap();
	}

	/**
	 * Initializes all the necessary data for this transform
	 *
	 * @param store1
	 *            store whose default feature type is the target of this
	 *            transform
	 *
	 * @param store2
	 *            store whose default feature type will provide the new
	 *            attributes to join
	 *
	 * @param keyAttr1
	 *            key attribute in store1 that matches keyAttr2 in store2
	 *            (foreign key), used for joining both stores.
	 *
	 * @param keyAttr2
	 *            key attribute in store2 that matches keyAttr1 in store2
	 *            (foreign key), used for joining both stores.
	 *
	 * @param attrs
	 *            names of the attributes in store2 that will be joined to
	 *            store1.
	 */
	public void initialize(FeatureStore store1, FeatureStore store2,
			String keyAttr1, String keyAttr2,
			String[] attrs)
			throws DataException {

		if (store1 == store2) {
			throw new IllegalArgumentException("store1 == store2");
		}

		// Initialize needed data
		this.setFeatureStore(store1);
		this.store2 = store2;
		this.keyAttr1 = keyAttr1;
		this.keyAttr2 = keyAttr2;
		this.attrs = attrs;

		// calculate this transform resulting feature type
		// by adding all specified attrs from store2 to store1's default
		// feature type
		this.originalFeatureType = this.getFeatureStore()
				.getDefaultFeatureType();

		EditableFeatureType type = this.getFeatureStore().getDefaultFeatureType().getEditable();

		FeatureType type2 = store2.getDefaultFeatureType();

		for (int i = 0; i < attrs.length; i++) {
			String name = attrs[i];

			// If an attribute already exists with the same name in store1's
			// default feature type,
			// calculate an alternate name and add it to our type
			int j = 0;
			while (type.getIndex(name) >= 0) {
				name = attrs[i] + "_" + ++j;
			}
			type.add(name,
					type2.getAttributeDescriptor(attrs[i]).getDataType());

			// keep correspondence between original name and transformed name
			this.targetNamesMap.put(attrs[i], name);
		}
		if (this.targetNamesMap.containsKey(keyAttr2)) {
			this.attrsForQuery = this.attrs;
		} else {
			ArrayList list = new ArrayList(this.attrs.length + 1);
			list.addAll(Arrays.asList(this.attrs));
			list.add(keyAttr2);
			this.attrsForQuery = (String[]) list.toArray(new String[] {});
		}

		// assign calculated feature type as this transform's feature type
		FeatureType[] types = new FeatureType[] { type.getNotEditableCopy() };
		setFeatureTypes(Arrays.asList(types), types[0]);
	}

	/**
	 *
	 *
	 * @param source
	 *
	 * @param target
	 *
	 * @throws DataException
	 */
	public void applyTransform(Feature source, EditableFeature target)
			throws DataException {

		// copy the data from store1 into the resulting feature
		this.copySourceToTarget(source, target);

		// ask store2 for the specified attributes, filtering by the key
		// attribute value
		// from the source feature
		JoinTransformEvaluator eval = this.getEvaluator();
		eval.updateValue(source.get(this.keyAttr1));

		FeatureQuery query = this.getFeatureStore().createFeatureQuery();
		query.setAttributeNames(attrsForQuery);
		query.setFilter(eval);

		FeatureSet set = store2.getFeatureSet(query);

		// In this join implementation, we will take only the first matching
		// feature found in store2

		FeatureAttributeDescriptor attr;
		Feature feat;
		String targetName;

		Iterator itAttr;

		DisposableIterator itFeat = set.iterator();
		if (itFeat.hasNext()) {
			feat = (Feature) itFeat.next();

			// copy all attributes from joined feature to target
			this.copyJoinToTarget(feat, target);
		}
		itFeat.dispose();
		set.dispose();
	}

	/**
	 * @param feat
	 * @param target
	 */
	private void copyJoinToTarget(Feature join, EditableFeature target) {
		Iterator iter = targetNamesMap.entrySet()
				.iterator();
		Entry entry;
		FeatureType trgType = target.getType();
		FeatureAttributeDescriptor attr;
		while (iter.hasNext()) {
			entry = (Entry) iter.next();
			attr = trgType.getAttributeDescriptor((String) entry.getValue());
			if (attr != null) {
				target.set(attr.getIndex(), join.get((String) entry.getKey()));
			}
		}


	}

	/**
	 * @param source
	 * @param target
	 */
	private void copySourceToTarget(Feature source, EditableFeature target) {
		FeatureAttributeDescriptor attr, attrTrg;
		FeatureType ftSrc = source.getType();
		FeatureType ftTrg = target.getType();


		for (int i = 0; i < source.getType().size(); i++) {
			attr = ftSrc.getAttributeDescriptor(i);
			if (ftTrg.getIndex(attr.getName()) > -1) {
				try {
					target.set(attr.getName(), source.get(i));
				} catch (IllegalArgumentException e) {
					attrTrg = ftTrg.getAttributeDescriptor(attr.getName());
					target.set(attrTrg.getIndex(), attrTrg.getDefaultValue());
				}

			}
		}

	}

	private JoinTransformEvaluator getEvaluator() {
		if (this.evaluator == null){
			this.evaluator = new JoinTransformEvaluator(keyAttr2);
		}
		return evaluator;

	}

	private class JoinTransformEvaluator implements Evaluator {

		private String attribute;
		private Object value;
		private String cql;
		private EvaluatorFieldsInfo info = null;

		//		private int attributeIndex;

		public JoinTransformEvaluator(String attribute) {
			this.attribute = attribute;
			this.value = null;
			this.info = new EvaluatorFieldsInfo();

			//			this.attributeIndex = attrIndex;
		}

		public void updateValue(Object value) {
			this.value = value;
			this.cql = this.attribute + "= '" + this.value + "'";
			this.info = new EvaluatorFieldsInfo();
			this.info.addMatchFieldValue(this.attribute, value);
		}

		public Object evaluate(EvaluatorData arg0) throws EvaluatorException {
			Object curValue = arg0.getDataValue(attribute);
			if (curValue == null) {
				return new Boolean(value == null);
			}
			return new Boolean(curValue.equals(value));
		}

		public String getCQL() {
			return this.cql;
		}

		public String getDescription() {
			return "Evaluates join transform match";
		}

		public String getName() {
			return "JoinTransformEvaluator";
		}

		public EvaluatorFieldsInfo getFieldsInfo() {
			return this.info;
		}

	}



	public void saveToState(PersistentState state) throws PersistenceException {
		state.set("store1", getFeatureStore());
		state.set("store2", store2);
		state.set("keyAttr1", keyAttr1);
		state.set("keyAttr2", keyAttr2);
		state.set("attrs", Arrays.asList(attrs));
	}

	public void loadFromState(PersistentState state) throws PersistenceException {
		List attrsTmp = (List) state.get("attrs");
		String[] aattrs = (String[]) attrsTmp.toArray(new String[attrsTmp.size()]);
		try {
			this.initialize(
					(FeatureStore) state.get("store1"),
					(FeatureStore) state.get("store2"), 
					state.getString("keyAttr1"),
					state.getString("keyAttr2"), 
					aattrs);
		} catch (DataException e) {
			throw new PersistenceException(e);
		}

	}


	public FeatureType getSourceFeatureTypeFrom(FeatureType arg0) {
		EditableFeatureType orgType = originalFeatureType.getEditable();
		Iterator iter = arg0.iterator();
		FeatureAttributeDescriptor attr;
		ArrayList toRetain = new ArrayList();
		while (iter.hasNext()) {
			attr = (FeatureAttributeDescriptor) iter.next();
			if (this.targetNamesMap.containsValue(attr.getName())) {
				continue;
			}
			toRetain.add(attr.getName());
		}

		if (!toRetain.contains(keyAttr1)) {
			toRetain.add(keyAttr1);
		}

		iter = originalFeatureType.iterator();
		while (iter.hasNext()) {
			attr = (FeatureAttributeDescriptor) iter.next();
			if (!toRetain.contains(attr.getName())) {
				orgType.remove(attr.getName());
			}

		}

		return orgType.getNotEditableCopy();
	}

	public boolean isTransformsOriginalValues() {
		return false;
	}
 }



Y para aplicar la transformacion hariamos algo como:

.. code-block:: java

  
  String[] attrNames = (...);

  FeatureStoreTransform transform = new JoinTransform().initialize(store, store2, "featId", "featId", attrNames);
  store.getTransforms().add(transform);
