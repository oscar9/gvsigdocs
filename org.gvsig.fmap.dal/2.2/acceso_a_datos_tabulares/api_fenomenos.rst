Acceso a los fenomenos
======================

Acceso básico
-------------

Dentro de este apartado veremos:

* Como recorrer los fenómenos de un shape.

* Como manejarse con un conjunto de fenómenos.

* Acciones asociadas a un :javadoc:`Feature <Feature>` para consultar
  los valores de sus atributos.


Veamos ahora a seguir este documento con un ejemplo simple. Accederemos a un fichero shape y recorreremos todos sus fenómenos.

Lo primero que tendremos que hacer será conseguir una instancia del :javadoc:`DataManager <DataManager>`
del API de acceso a datos. Esto se consigue mediante la llamada al método 
estático, *getDataManager* de la clase :javadoc:`DALLocator <DALLocator>`.

.. code-block:: java

  manager = DALLocator.getDataManager();

Una vez conseguido la instancia del :javadoc:`DataManager <DataManager>` , utilizaremos este para conseguir
una instancia de los *parámetros*  que necesitamos para acceder al *almacén* de
nuestro fichero *shape*. Para eso invocaremos al método *createStoreParameters*
indicándole el tipo de almacén al que queremos acceder. 

.. code-block:: java

  params = manager.createStoreParameters("Shape");

En el caso de un *almacén* de tipo *shape*, el único parámetro que necesitaremos
será el nombre del fichero al que queremos acceder. Los *parámetros* se comportan
como un *Map*, así que asignaremos a la clave *shpfilename*, el nombre del fichero al
que queremos acceder. 

.. code-block:: java

  params.setDynValue("shpfilename","data/prueba.shp");

Una vez tenemos los *parámetros* del *almacén* al que queremos acceder preparados, 
le indicaremos al *manager* que nos cree un :javadoc:`DataStore <DataStore>` acorde a esos parámetros que
nos permita acceder a nuestro fichero. Esto se hará llamando al método *createStore*
pasándole como argumento los *parámetros* que ya hemos preparado. Esta llamada nos 
devolverá un :javadoc:`FeatureStore <FeatureStore>` , y ya con el podremos acceder a sus fenómenos.

.. code-block:: java

  store = (FeatureStore)manager.createStore(params);

Cuando ya disponemos del *store*, podemos utilizar este para acceder a sus fenómenos.
La forma mas simple de hacer esto seria visitar el almacen.

.. code-block:: java

  store.accept( new Visitor() {
      public void visit(Object obj) {
        Feature feature = (Feature)obj;
        ...
      }
    }
  );

Como alternativa a este metodo podemos usar el método *getFeatureSet*,
que nos devolverá un conjunto de fenómenos, :javadoc:`FeatureSet <FeatureSet>` , con el que podremos
operar. 

.. code-block:: java

  features = store.getFeatureSet();

Es importante entender que estas son las unicas formas de acceder a los
fenómenos de un *almacén*. Además estas nos
permiten dado un *almacén de datos* acceder de forma simultanea a sus fenómenos invocando desde
varias partes de la aplicación al método *accept* o *getFeatureSet* para obtener distintos conjuntos de datos.
Como norma general no deberemos usar un conjunto de fenómenos, :javadoc:`FeatureSet <FeatureSet>` . 
desde varias partes de la aplicación simultáneamente. Si necesitamos acceder a los
datos del *almacén*, allá donde se necesite, se creará un nuevo conjunto de datos.

Los principales métodos de un :javadoc:`FeatureSet <FeatureSet>` son:

* *getDefaultFeatureType*

* *getFeatureTypes*

* *isEmpty*

* *getSize*

* *dispose*

* *iterator*

* *fastiterator*

Hacer una mención especial sobre los dos últimos métodos, *iterator* y 
*fastiterator*. El primero, *iterator*, devuelve una nueva instancia de :javadoc:`Feature <Feature>`
cada vez que se invoca al método *next*. El segundo, *fastiterator* devuelve
siempre la misma instancia de :javadoc:`Feature <Feature>` al invocar al método *next*, alterando
el valor de esta de forma que está cargada con los valores del nuevo fenómeno.
Este comportamiento nos ahorra crear tantos objetos :javadoc:`Feature <Feature>` como elementos
tenga el conjunto, pero deberemos tener especial cuidado en no guardarnos
una referencia a los objetos :javadoc:`Feature <Feature>` así obtenidos ya que irán alterando su valor
conforme se itere sobre el conjunto.

El :javadoc:`FeatureSet <FeatureSet>`, ademas de los metodos para iterar sobre el tambien nos permite
visitarlo, siendo siempre recomendable utilizar este mecanismo frente al recorrido
mediante un iterador.

Volviendo al ejemplo sobre el que estábamos trabajando, 
podemos obtener un iterador sobre el conjunto de todas los fenómenos
y recorrerlos. En el ejemplo accederemos al valor del atributo *NOMBRE* 
del fenómeno y lo sacaremos por la salida estándar.

.. code-block:: java

  DisposableIterator it = features.iterator();
  while( it.hasNext() ) {
    feature = (Feature)it.next();
    System.out.println(feature.getString("NOMBRE"));
  }
  it.dispose();

Tenemos también la opción de saltar directamente a una posición, pidiendo el :javadoc:`DisposableIterator <DisposableIterator>`
con un índice. Podemos usar el :javadoc:`DisposableIterator <DisposableIterator>` devuelto de la misma forma que con
el :javadoc:`DisposableIterator <DisposableIterator>` normal, aunque el primer elemento devuelto será el que ocupa la posición
correspondiente al índice indicado:

.. code-block:: java
 
  DisposableIterator it = features.iterator(100);
  while( it.hasNext() ) {
    feature = (Feature)it.next();
    System.out.println(feature.getString("NOMBRE"));
  }
  it.dispose();
  

Por último una vez hemos terminado de trabajar con el conjunto de fenómenos y el *almacén*
debemos informarles de ello, para que este libere todos los recursos que esté utilizando.

.. code-block:: java

  features.dispose();
  store.dispose();

Es importante tener en cuenta algunas consideraciones. 

Los elementos de un :javadoc:`FeatureSet <FeatureSet>` son siempre del tipo :javadoc:`Feature <Feature>` , pero si no 
hemos indicado nada al respecto cuando creamos el conjunto, no deberemos asumir
que todos tienen la misma estructura, ya que dependiendo del tipo de *almacén* podemos
encontrarnos con que la estructura de estos puede cambiar de un elemento a otro. Más adelante 
explicaremos como filtrar los conjuntos de fenómenos de forma que el tipo de elementos
sea único dentro de este.

Otro punto a tener en cuenta está relacionado con la implementación del recorrido 
de los elementos de un conjunto. No deberemos asumir nada al respecto de esto. El
API no fija nada respecto a como debe implementar cada *almacén* el recorrido de sus
elementos. Puede haber *almacenes* que carguen todos sus elementos en memoria para ser recorridos
mientras que otros sólo mantengan en memoria el elemento que se va a devolver o incluso
otros que utilicen algoritmos de *cache* específicos para acelerar su acceso. En general
la implementación de los distintos *almacenes* que lleva de base esta librería tiende a
ser lo mas óptima posible, balanceando la carga en memoria y la velocidad de acceso, e
implementándose de una u otra forma en función de las características del *almacén*.

Por último es importante invocar a la acción *dispose* del conjunto de fenómenos cuando
dejemos de trabajar con él, así como de los :javadoc:`DisposableIterator <DisposableIterator>` cuando dejemos de usarlos.
Dependiendo del tipo de *almacén*, esto puedo liberar recursos como ficheros abiertos en disco
o conexiones a BBDD. Así mismo cuando hayamos creado nosotros el *almacén*, debemos de 
encargarnos de invocar a la acción *dispose* sobre este. Hay que
tener cuidado de no invocar a la acción *dispose* sobre un *almacén* asociado a una capa de
gvSIG. Será la propia capa de gvSIG la encargada de hacerlo cuando no lo necesite.

Veamos todo el código del ejemplo junto:

.. code-block:: java

  DataManager manager;
  DataStoreParameters params;
  FeatureStore store;
  FeatureSet features;
  Feature feature;

  manager = DALLocator.getDataManager();
  params = manager.createStoreParameters("Shape")
  params.setDynValue("shpfilename","data/prueba.shp");
  store = (FeatureStore)manager.createStore(params);
  features = store.getFeatureSet();

  DisposableIterator it = features.iterator();
  while( it.hasNext() ) {
    feature = (Feature)it.next();
    System.out.println(feature.getString("NOMBRE"));
  }
  it.dispose();
  features.dispose();
  store.dispose();

Aunque siempre se pueden utilizar iteradores para recorrer los elementos del
almacen, es recomendable cuando sea posible utilizar *visitors*. Estos nos
garantizan que se libreran los recursos de forma automatica sin tener que 
ir invocando a los metos *dispose* del iterador o el conjunto de fenomenos.
El codigo usando *visitors* quedaria algo como:

.. code-block:: java

  DataManager manager;
  DataStoreParameters params;
  FeatureStore store;
  FeatureSet features;
  Feature feature;

  manager = DALLocator.getDataManager();
  params = manager.createStoreParameters("Shape")
  params.setDynValue("shpfilename","data/prueba.shp");
  store = (FeatureStore)manager.createStore(params);
  store.accept( new Visitor() {
      public void visit(Object obj) {
        Feature feature = (Feature)obj;
        System.out.println(feature.getString("NOMBRE"));
      }
    }
  );
  store.dispose();



Hasta aquí hemos visto como conseguir usar el API para llegar a obtener un objeto
:javadoc:`Feature <Feature>` . Pero, ¿ qué es un :javadoc:`Feature <Feature>` y qué servicios nos ofrece ?

La clase :javadoc:`Feature <Feature>` representa a un fenómeno dentro del almacén. En él se aglutina 
toda la información, ya sea alfanumérica o vectorial. Un :javadoc:`Feature <Feature>` se comporta
como un *Map* muy especializado en el que las *key* hacen referencia a los nombres 
de los atributos del fenómeno y los *value* de estas al valor de estos atributos. Cada
atributo tiene su tipo, por lo que no hay limitación en cuanto a cuantos atributos
de tipo vectorial puede tener la definición de una :javadoc:`Feature <Feature>` .

En :javadoc:`Feature <Feature>` nos encontraremos un método *get* para acceder a los valores de los atributos 
del fenómeno. Para facilitar el acceso se han añadido métodos de utilidad que nos
devuelven ya tipos concretos en lugar de *Object*. Así, en el ejemplo anterior, 
se usa el *getString* para obtener el valor del atributo *NOMBRE* como un *String*.


Los métodos de acceso a los valores de los atributos de un :javadoc:`Feature <Feature>` son:

* *get*, retorna el valor del atributo indicado como
  un *Object*.

* *getInt*, retorna el valor del atributo como
  un valor de tipo *int*.

* *getBoolean*, retorna el valor del atributo como
  un valor de tipo *boolean*.

* *getLong*, retorna el valor del atributo como
  un valor de tipo *long*.

* *getFloat*, retorna el valor del atributo como
  un valor de tipo *float*.

* *getDouble*, retorna el valor del atributo como
  un valor de tipo *double*.

* *getDate*, retorna el valor del atributo como
  un valor de tipo *Date*.

* *getString*, retorna el valor del atributo como
  un valor de tipo *String*.

* *getByte*, retorna el valor del atributo como
  un valor de tipo *byte*.

* *getGeometry*, retorna el valor del atributo como
  un valor de tipo :javadoc:`Geometry <Geometry>`.

* *getFeature*, retorna el valor del atributo como
  un valor de tipo :javadoc:`Feature <Feature>` .

* *getArray*, retorna el valor del atributo como
  un valor de tipo *Object[]*.

Para los tipos básicos, si el valor del
atributo pedido no es del tipo pedido se intentará
convertir a ese tipo y en caso de que no se pueda
se lanzará una excepción.

Conviene comentar sobre los últimos tres métodos.

En lo que se refiere al acceso a datos, los valores
de datos de tipo vectorial los trata como objetos de tipo
:javadoc:`Geometry <Geometry>` . Este tipo de datos esta definido en la librería
de geometrías de gvSIG, *org.gvsig.fmap.geom* para más información
sobre las funcionalidades alrededor de este tipo consulte la
documentación de esta librería (ver en documentos relacionados).

En lo que respecta al método *getFeature*, está pensado para
dar soporte a fuentes de datos en las que un fenómeno dentro de
la fuente de datos tiene un atributo que a su vez es una estructura
compleja, con sus propios atributos y valores. La forma de
recuperar ese atributo *compuesto*, por referirnos a él de alguna
forma, será a través de este método.

En lo que respecta al método *getArray* viene a cubrir el hueco de
qué pasa cuando un atributo de un fenómeno está compuesto por una tupla
de valores. Para acceder a esta tupla de valores se usará este método
que nos la presentará como un array.

Ahora unas consideraciones sobre rendimientos.
En general es recomendable usar el nombre del atributo para acceder
a el valor de estos, ya que, para cada consulta podemos variar tanto en
orden como en cantidad los atributos de la :javadoc:`Feature <Feature>` que deseamos. Sin embargo
en ocasiones puede resultar mas óptimo usar su *índice* para acceder 
a él. Todos los métodos de acceso que acabamos de comentar están sobrecargados
de forma que podemos usar bien el nombre o bien su *índice*.

Veamos como podía usarse esta forma de acceso a los atributos en el
ejemplo anterior.

.. code-block:: java

  DisposableIterator it = features.iterator();
  while( it.hasNext() ) {
    feature = (Feature)it.next();
    featureType = feature.getType();
    index = featureType.getIndex("NOMBRE");
    System.out.println(feature.getString(index));
  }
  it.dispose();

Evidentemente, el cambio, así introducido no ha supuesto ninguna mejora
en el rendimiento. Ahora bien si asumimos que estamos trabajando con
un almacén de datos que sólo soporta un tipo de :javadoc:`Feature <Feature>` , como es
nuestro caso, podemos optimizar algo mas el acceso.

.. code-block:: java

  featureType = store.getDefaultFeatureType();
  index = featureType.getIndex("NOMBRE");

  DisposableIterator it = features.iterator();
  while( it.hasNext() ) {
    feature = (Feature)it.next();
    System.out.println(feature.getString(index));
  }
  it.dispose();

Esto sí que puede resultar en una ganancia considerable en rendimientos
cuando estemos accediendo a un *almacén* con un numero muy grande de
fenómenos.

Cabe resaltar que hemos utilizado en el ejemplo un par de métodos nuevos.
Por un lado podemos ver como dada un :javadoc:`Feature <Feature>` accedemos a su *tipo* mediante
el método *getType*, que nos devuelve un objeto del tipo :javadoc:`FeatureType <FeatureType>` .
Y por otro lado, podemos obtener el *tipo* de los fenómenos de un *almacén*
mediante el método `getDefaultFeatureType` . Hay que tener cuidado con el
uso de este método ya que cuando estemos trabajando con *almacenes* que
contengan :javadoc:`Feature <Feature>` de varios tipos, nos dará de forma arbitraria sólo
un tipo. Si queremos saber los tipos de fenómenos que contiene un
*almacén* deberemos invocar a `getFeatureTypes` que nos devolverá una
lista de los :javadoc:`FeatureType <FeatureType>` que tiene el *almacén*.

Puede ser conveniente repasar la referencia sobre:

* :javadoc:`FeatureSet <FeatureSet>`

* :javadoc:`Feature <Feature>`

* :javadoc:`FeatureType <FeatureType>`



Filtrado
--------


Dentro de este apartado veremos:

* Como obtener un conjunto de fenómenos filtrando
  por tipo de fenómeno.

* Como obtener un conjunto de fenómenos filtrando
  por una condición en función de los atributos
  de los fenómenos.


Vamos a seguir trabajando sobre el ejemplo que hemos estado viendo en el
apartado anterior.  ¿ Como podemos obtener un subconjunto de fenómenos de
nuestro almacén ?

Para aplicar un filtro, este se aplicara en el momento de invocar al
método *getFeatureSet* de nuestro *almacén*. Para esto deberemos
construir un objeto :javadoc:`FeatureQuery <FeatureQuery>` , rellenarlo con los valores con los
que queremos filtrar e invocar al *getFeatureSet* pasándole
ese *query*.

A la hora de filtrar los fenómenos de un almacén de datos, podemos hacerlo
llevándonos de dos tipos de criterios:

* Según el tipo de fenómeno

* O según una condición de filtro en función de los valores
  de los fenómenos del almacén.

Por ejemplo podríamos asegurarnos que cuando nos recorremos los fenómenos
siempre nos da fenómenos del mismo tipo, para ello podríamos hacer:

.. code-block:: java

  types = store.getFeatureTypes();
  Iterator typesIterator = types.iterator();
  while( typesIterator.hasNext() ) {
    featureType = (FeatureType)typesIterator.next();
    index = featureType.getFieldIndex("NOMBRE");

    FeatureQuery query = store.createFeatureQuery();
    query.setFeatureType(featureType);
    features = store.getFeatureSet(query);

    DisposableIterator featuresIterator = features.iterator();
    while( featuresIterator.hasNext() ) {
      feature = (Feature)featuresIterator.next();
      System.out.println(feature.getString(index));
    }
    featuresIterator.dispose();
    features.dispose();
  }


Primero averiguamos los tipos de fenómenos que contiene nuestro almacén,
y luego obtenemos los fenómenos filtrando por tipo. Para filtrar creamos
un :javadoc:`FeatureQuery <FeatureQuery>` a partir del *store*.

.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();

Indicamos que queremos filtrar por los fenómenos de un tipo
en concreto.

.. code-block:: java

    query.setFeatureType(featureType):

y por ultimo creamos el conjunto de fenómenos usando ese
filtro.

.. code-block:: java

    features = store.getFeatureSet(query);

Al igual que podiamos emplear *visitor* sobre un almacen para 
acceder a sus fenomenos, cuando estamos aplicando un filtro 
tambien podemos usar el concepto de *visitor*, ya que el 
almacen dispone de un metodo *acept* que recibe un query.

.. code-block:: java

  store.accept( new Visitor() {
      public void visit(Object obj) {
        ..
      }
    },
    query 
  );

De forma similar a como filtramos por el tipo de fenómeno podemos
filtrar por una expresión en función de los atributos de los
fenómenos. Para ello deberemos crear un objeto :javadoc:`Evaluator <Evaluator>` con la
condición que deseemos. Así el código de filtrado podría ser:

.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();
    query.setFilter( manager.createExpresion("NOMBRE like 'a%'") );
    features = store.getFeatureSet(query);

    ...

    features.dispose();

Hay que tener en cuenta que podemos mezclar los dos tipos de filtro,
filtrando a su vez por los tipo de fenómeno y por una expresión.

.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();
    query.setFilter( manager.createExpresion("NOMBRE like 'a%'") );
    query.setFeatureType(featureType):
    features = store.getFeatureSet(query);
    
    ...

    features.dispose();

A la hora de aplicar un filtro a un *query*, debemos entregarle un objeto
de tipo :javadoc:`Evaluator <Evaluator>` . El :javadoc:`DataManager <DataManager>`  dispone de un evaluador por defecto
para la evaluación de expresiones, pero se pueden utilizar evaluadores
especializados para obtener mejores rendimientos.

Puede ser conveniente repasar la referencia sobre:

* :javadoc:`FeatureQuery <FeatureQuery>`

* :javadoc:`Evaluator <Evaluator>`

* :javadoc:`DataManager <DataManager>`



Ordenación
----------

La ordenación de los fenómenos devueltos por *getFeatureSet* se realiza
de forma similar a como se aplican los filtros. El orden que queremos aplicar
a nuestra colección de fenómenos se indica al :javadoc:`FeatureQuery <FeatureQuery>` a través del
método *getOrder*, que nos devuelve una objeto :javadoc:`FeatureQueryOrder <FeatureQueryOrder>` .

Así para ordenar los registros de forma ascendente por el atributo "NOMBRE"
haríamos:

.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();
    query.getOrder().add("NOMBRE",true);
    query.setFilter( manager.createExpresion("NOMBRE like 'a%'") );
    features = store.getFeatureSet(query);

    ...

    features.dispose();

Nótese que se pueden especificar simultáneamente tanto condiciones de filtrado por
expresión, por tipo de fenómeno y a su vez con o sin una ordenación.

Si quisiésemos hacer una ordenación descendente en lugar de usar:

.. code-block:: java

    order.add("NOMBRE",true);

Usaríamos

.. code-block:: java

    order.add("NOMBRE",false);


También es posible usar un :javadoc:`Evaluator <Evaluator>` para la ordenación. Por ejemplo,
podemos hacer que la ordenación no distinga mayúsculas de las minúsculas así:

.. code-block:: java

    query.getOrder().add(manager.createExpresion("lower(NOMBRE)"),true);

    
Contexto
--------

En ocasiones nos encontramos que dependiendo de algunas condiciones ajenas
a los datos en si mismos, no seria necesario disponer de un conjunto completo
de los fenómenos de un *almacén*, y si el *almacén* conociese cierta
información podría realizar optimizaciones en como recoge o recorre los
fenómenos que contiene para devolverlos a la aplicación. Esta información
adicional que no forma parte de los filtros u ordenaciones es lo que
denominamos contexto.

Nótese que cuando se indica una información de contexto para que el *almacén*
haga uso de ella y optimizar el acceso a los datos, la información que
nos devuelve la consulta no sera rigurosa. Por ejemplo, cuando estamos accediendo
a un servicio remoto de recuperación de fenómenos para pintarlos, si suministramos
a la consulta información de la escala, este puede obviar traerse del servidor
los fenómenos que no sean representativos de la escala a la que se este
trabajando e incluso simplificar las geometrías para optimizarlas en
función de la escala a la que van a ser usadas. Esto constituiría una optimización
durante el pintado de los datos, pero la información descargada nadie nos asegura
que fuese rigurosa. Así cuando vallamos a usar los datos para aplicar geoprocesos
o estemos interesados en realizar edición sobre ellos, nunca deberíamos indicar
en la consulta información de contexto.

Actualmente la única información de contexto prefijada que se trata en la
librería de acceso a datos es la *escala*. Se puede indicar información de contexto
adicional a modo de pares clave-valor. Hay que tener en cuenta que aunque se
especifique información de contexto, si el *almacén* no sabe como tratar
esa información de contexto, esta no se usará.

Imaginemos que estamos trabajando con capas de puntos obtenidas a partir de
un vuelo LIDAR. La cantidad de fenómenos asociado a esa fuente de datos puede
ser altísima, así que a la hora de pintar esos fenómenos nos gustaría que
tratase de hacer todas las optimizaciones que pudiese y, muy probablemente,
el uso de la información de escala a la cual voy a usar esos datos permita
acelerar mucho su acceso. Así podríamos indicar esa información al
obtener la colección de fenómenos.


.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();
    query.setScale(scale);
    features = store.getFeatureSet(query);

    ...

    features.dispose();

Esta información de contexto podría acumularse a las otras informaciones de
ordenación o filtrado de una consulta.

.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();
    query.getOrder().add("NOMBRE",true);
    query.setFilter( manager.createExpresion("NOMBRE like 'a%'") );
    query.setScale(scale);
    features = store.getFeatureSet(query);

    ...

    features.dispose();

Otra forma alternativa de indicar la escala como información de contexto seria:

.. code-block:: java

    FeatureQuery query = store.createFeatureQuery();
    query.getOrder().add("NOMBRE",true);
    query.setFilter( manager.createExpresion("NOMBRE like 'a%'") );
    query.setQueryParameter("Scale",scale);
    features = store.getFeatureSet(query);

    ...

    features.dispose();

De esta forma podrían indicarse otros valores de contexto que el
*almacén de datos* pueda utilizar para optimizar su consulta.

Es de recalcar que nadie nos garantiza que el *almacén* sepa hacer uso
de esa información para optimizar el acceso a los datos. Simplemente
ponemos a disposición del *almacén* esa información por si le puede ser
de utilidad. Y que si especificamos información de contexto, los datos
que devuelve la consulta no serán rigurosos.
