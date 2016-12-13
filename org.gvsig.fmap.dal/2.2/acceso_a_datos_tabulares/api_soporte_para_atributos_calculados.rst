 
Soporte para atributos calculados
=================================
   
.. _creación de un nuevo almacén: api_creacion_de_un_nuevo_almacen.html
.. _getDefaultFeatureType: http://downloads.gvsig.org/download/gvsig-desktop-testing/dists/2.3.0/javadocs/html/org/gvsig/fmap/dal/feature/FeatureStore.html#getDefaultFeatureType()
.. _getFeatureTypes: http://downloads.gvsig.org/download/gvsig-desktop-testing/dists/2.3.0/javadocs/html/org/gvsig/fmap/dal/feature/FeatureStore.html#getFeatureTypes()

En este apartado veremos:

* Como añadir un atributo calculado simple, basado
  en una expresión de cadena.

* Algunas consideraciones sobre la edición y los
  atributos calculados.

* Como crear un campo calculado complejo que requiera
  un evaluador especializado.


En el apartado `creación de un nuevo almacén`_ vimos como crear un
alamcén en formato *dbf* en el que teníamos un atributo *POBLACION* y 
otro *AREA*.

Puede ser interesante disponer del valor de densidad de población en 
ese mismo almacén. En lugar de añadirle un nuevo atributo con el valor de
la densidad de población podemos añadirle un atributo que calcule la
densidad de población en función de los campos *AREA* y *POBLACION*.

Veamos como podríamos hacer esto.

.. code-block:: java

  store.edit();
  FeatureType featureType = store.getDefaultFeatureType();
  EditableFeatureType editableFeatureType = featureType.getEditable();
  editableFeatureType.add("DENSIDAD_POBLACION",
          DataTypes.DOUBLE, 
          manager.createExpresion("POBLACION / AREA")
  );
  store.update(editableFeatureType);
  store.finishEditing();

Para añadir el nuevo atributo, deberemos añadir a la descripción del
fenómeno del almacén un nuevo atributo. Veamos una breve explicación
de los pasos que hemos seguido para realizar esta operación:

#. Puesto que vamos a realizar una modificación sobre
   el almacén, deberemos asegurarnos que este se encuentra
   en modo edición.

#. Invocaremos a getDefaultFeatureType_ para obtener la descripción
   de los fenómenos del almacén. Una vez dispongamos
   de el, obtendremos una versión *editable* de este, ya que el que nos
   devuelve getDefaultFeatureType_ es de solo consulta.

#. Invocaremos a su método *add* para añadirle el nuevo atributo.
   a este le indicaremos:

   * El nombre del atributo a añadir, *DENSIDAD_POBLACION*.
   * El tipo de datos asociado al atributo, *TYPE_DOUBLE*.
   * Un evaluador que resuelva la expresión que deseamos.
     El :javadoc:`DataManager <DataManager>` nos suministrara un constructor por
     defecto para evaluadores de expresiones mediante su método
     *createExpresion*, asi que le diremos que la expresión que
     deseamos que tenga sera "POBLACION / AREA".

#. invocaremos al método *update* del almacén, :javadoc:`FeatureStore <FeatureStore>`, 
   con el :javadoc:`FeatureType <FeatureType>` modificado para que se apliquen los cambios.

#. Por ultimo, si hemos iniciado la edición para realizar esta
   operación la terminaremos.


Hay que tener en cuenta una consideración respecto a la adición de
campos calculados a un almacén. Para realizar esta operación es preciso
entrar en edición del almacén y finalizarla al terminar de añadir
los campos calculados. Sin embargo, la operación de inserción de
campos calculados no afecta a los datos del almacén ni a la 
estructura de este. Es decir, al finalizar al edición no se realiza
ninguna modificación sobre el almacén físico de datos. Los cambios 
quedan únicamente en la definición en memoria de este. Se trata de 
una edición que provoca unos cambios *ligeros* o *suaves* por decirlo 
de alguna manera. Cuando la edición provoca cambios de este tipo, no
precisa que el almacén soporte escritura. Es decir podemos hacer este
tipo de operaciones de edición sobre almacenes aunque la invocación a 
*allowWrite* sobre el almacén indique que no esta soportada la escritura.

Veamos ahora un ejemplo algo mas complejo de atributo calculado.
En el ejemplo anterior utilizábamos el evaluador de expresiones que
suministra por defecto el :javadoc:`DataManager <DataManager>`. Ahora bien, puede suceder que
este evaluador de expresiones no nos de la capacidad de calculo que
necesitemos para nuestro atributo. En estos casos podemos crear
nuestro propio evaluador a medida de las operaciones que deseemos.
Veamos esto con un ejemplo.

Supongamos que tenemos un fichero *dbf* en el que tenemos dos atributos,
*X* e *Y* con las coordenadas x e y de un punto, y quisiésemos representar
esos puntos como tales en lugar de como dos simples números. Podríamos
añadir un atributo calculado que nos devolviese una geometría construida
a partir de los valores de los campos *X* e *Y*. Veamos el ejemplo, por
un lado necesitaremos crear nuestro evaluador:

.. code-block:: java

  class XY2Geometry extends AbstractEvaluator {
       private String xname;
       private String yname;
       private GeometryManager geomManager;

       public XY2Geometry initialize(String xname, String yname) {
          this.xname = xname;
          this.yname = yname;
          geomManager = GeometryLocator.getGeometryManager();
          return this;
      }

      public Object evaluate(EvaluatorData data) throws EvaluatorException {
          Double x = (Double) data.getDataValue(this.xname);
          Double y = (Double) data.getDataValue(this.yname);
          Geometry geom;
          try {
              geom = geomManager.createPoint(x.doubleValue(), y
                  .doubleValue(), Geometry.SUBTYPES.GEOM2D);
          } catch (CreateGeometryException e) {
              throw new EvaluatorException(e);
          }
          return geom;
      }

      public String getName() {
          return "XY2Geometry";
      }
  }


La clase abstracta :javadoc:`AbstractEvaluator <AbstractEvaluator>`, nos deja dos métodos por implementar:

* *getName*, que deberá devolver un nombre descriptivo de la operación
  que vallamos a hacer.

* *evaluate*, que recibe un :javadoc:`EvaluatorData <Evaluator>`, en el que recibiremos los 
  valores del resto de atributos del fenómeno. Este método deberá ser
  el encargado de realizar los cálculos y devolver el resultado de estos.
  En nuestro caso, obtendrá los valores de los campos x e y, y con ellos
  construirá una geometría de tipo punto que sera lo que devuelva.

Además de estos métodos, le hemos añadido un método *initialize* en el
que le pasamos los parámetros que necesita para realizar correctamente los
cálculos. Es recomendable disponer de un método de inicialización separado
del constructor ya que eso nos puede facilitar las tareas de serialización
del objeto en caso de que lo necesitemos.

Y una vez dispongamos de un evaluador adecuado podemos añadir ya
nuestro atributo calculado al almacén:

.. code-block:: java

  store.edit();
  FeatureType featureType = store.getDefaultFeatureType();
  EditableFeatureType editableFeatureType = featureType.getEditable();
  editableFeatureType.add("GEOM",
    DataTypes.GEOMETRY, 
    new XY2Geometry().initialize("X", "Y")
  );
  store.update(editableFeatureType);
  store.finishEditing();

En este caso, a diferencia de nuestro ejemplo anterior sobre la densidad
de población, en la invocación al método *add*, le indicaremos:

* El nombre del nuevo atributo que añadimos, *GEOM*.
* Que su tipo de datos, en lugar de ser *DOUBLE*, ahora es 
  un *GEOMETRY*.
* Y que en lugar de invocar al :javadoc:`DataManager <DataManager>` para obtener un
  evaluador de expresiones de cadena, construimos una instancia
  de nuestro evaluador, *XY2Geoemtry*, la inicializamos con los
  valores de los nombres de los campos que contienen la x e y, y
  se la pasamos como el evaluador a usar en ese campo.

