Consultando la estructura de un almacen
=======================================
   
.. _getDefaultFeatureType: http://downloads.gvsig.org/download/gvsig-desktop-testing/dists/2.3.0/javadocs/html/org/gvsig/fmap/dal/feature/FeatureStore.html#getDefaultFeatureType()
.. _getFeatureTypes: http://downloads.gvsig.org/download/gvsig-desktop-testing/dists/2.3.0/javadocs/html/org/gvsig/fmap/dal/feature/FeatureStore.html#getFeatureTypes()


Para consultar la estructura de los fenómenos de un almacén, :javadoc:`FeatureStore <FeatureStore>`, 
dispone de dos acciones:

* getDefaultFeatureType_, que devuelve el tipo de fenómeno
  por defecto del almacén

* getFeatureTypes_ que devuelve una lista con todos los tipos
  de fenómeno que hay en el almacén.

Las primeras preguntas que tendemos ha hacernos cuando vemos estos métodos
son:

* ¿ Qué es eso del tipo de fenómeno por defecto ?

* ¿ Por qué una lista de tipos de fenómenos ?

Las dos preguntas van estrechamente ligadas.

Normalmente estamos acostumbrados a encontrarnos *almacenes* como
ficheros *shapes*, *dxf*, *dgn* o bases de datos como *postgreSQL* en los que
los fenómenos tienen una estructura común a todo el almacén. Sin embargo
hay escenarios en los que eso puede no ser así. Por ejemplo, podemos encontrarnos
un fichero GML en el que los fenómenos tienen dos, tres o mas estructuras distintas
a lo largo el fichero. La forma de consultar cual es la estructura de los
fenómenos seria a través del método `getFeatureTypes` , que nos dará una lista
con un :javadoc:`FeatureType <FeatureType>` por cada tipo de fenómeno del almacén. 
Como durante mucho tiempo hemos manejado almacenes que solo soportan un tipo
de fenómeno, se ha introducido el método `getDefaultFeatureType` en el almacén para
facilitar el acceso al :javadoc:`FeatureType <FeatureType>` cuando solo existe un tipo de fenómeno. 

¿ Y qué pasa si invocamos al *getDefaultFeatureType* sobre un almacén con varios
tipos de fenómenos ?

Este nos devolverá un :javadoc:`FeatureType <FeatureType>` de forma arbitraria dependiendo del tipo de 
almacén con el que estemos trabajando.

En general, a no ser que estemos seguros de que solo hay un tipo de fenómenos en el 
almacén deberemos usar `getFeatureTypes` para acceder a la estructura de estos.
Siempre podemos consultar el *size* de la lista para determinar cuantos tipos de 
fenómeno tenemos.

Una vez hemos conseguido un :javadoc:`FeatureType <FeatureType>`, podemos pensar en el como en una lista de
descriptores de los atributos del fenómeno, disponiendo de un método *size* que nos
dice cuantos atributos tiene el fenómeno que describe, un método *get* que nos 
devolverá un :javadoc:`FeatureAttributeDescriptor <FeatureAttributeDescriptor>`, por nombre o índice, o un iterador sobre
estos.

Veamos cuales son las principales acciones que podemos encontrarnos en un :javadoc:`FeatureType <FeatureType>`
que nos permitan interrogarle para saber de la estructura
del fenómeno:

* *size*, nos devuelve el numero de atributos que tiene el fenómeno.

* *get*, que dándole un nombre o un índice de atributo nos devolverá
  un descripción de las propiedades del atributo, :javadoc:`FeatureAttributeDescriptor <FeatureAttributeDescriptor_>` . 

* *getIndex*, que dándole un nombre de atributo nos devuelve el índice
  de este.

* *getDefaultGeometryAttributeName*, nos da el nombre del atributo que es
  la geometría por defecto de este almacén. Normalmente gvSIG pintará esta
  geometría cuando tenga que obtener una representación gráfica del almacén.

* *getDefaultGeometryAttributeIndex*, nos da el índice del atributo que es
  la geometría por defecto de este almacén.

* *getDefaultSRS*. Nos devuelve la cadena que representa al SRS de la geometría
  por defecto.

* *getSRSs*. Un fenómeno puede contener mas de un atributo de tipo geometría, 
  y cada atributo de tipo geometría puede tener un SRS diferente. Este método
  nos devolverá una lista con los SRSs de todos los atributos de tipo geometría.

* *iterator*. Nos devuelve un iterador sobre los descriptores de atributos del
  fenómeno.

Conviene también dar un vistazo, aunque sea superficial, a los principales métodos
u operaciones que podemos encontrar en el :javadoc:`FeatureAttributeDescriptor <FeatureAttributeDescriptor_>`.

* *getName*, que nos informara sobre el nombre del atributo.

* *getDataType* y *getDataTypeName*, que nos dirá de que tipo son los 
  datos asociados al atributo. La lista de tipos soportados están definidos
  en el interface :javadoc:`DataTypes <DataTypes>` .

* *getDefaultValue*, nos dirá cual es el valor por defecto del atributo.

* *getIndex*, nos dirá el índice numérico asociado al atributo.

* *getSize*, para los tipos de datos que lo precisen , por ejemplo las
  cadenas, nos informa de cual es su tamaño.

* *getEvaluator*, que nos devolverá una instancia del :javadoc:`Evaluator <Evaluator>` asociado
  al atributo, en caso de que este sea un atributo calculado.

* *allowNull*, nos informa de si ese atributo permite o no valores nulos.

* *isPrimaryKey*, nos dirá si ese atributo forma parte de la clave
  primaria que usa el proveedor de datos para identificar al fenómeno.

* *isReadOnly*, que nos dirá si ese atributo puede ser actualizado o no.


Veamos ahora un pequeño ejemplo en el que se nos muestra como podemos obtener
el nombre de los atributos y su tipo de datos asociados a un :javadoc:`FeatureType <FeatureType>`.

.. code-block:: java

  FeatureType featureType = store.getDefaultFeatureType();
  Iterator it = featureType.iterator();
  while( it.hasNext() ) {
    attribute = (FeatureAttributeDescriptor)it.next();
    System.out.print(attribute.getDataTypeName();
    if( attribute.getSize() > 1 ) {
      System.out.print("["+attribute.getSize()+"]");
    }
    System.out.print(" " + attribute.getName() );
    Evaluator eval = attribute.getEvaluator();
    if( eval != null ) {
      System.out.print(attribute.getName()+", "+attribute.getDataTypeName()+" -- Calculado "+ eval.getCQL());
    } 
    System.out.println();
  }
