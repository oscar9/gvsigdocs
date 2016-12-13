 
Modificación y escritura de datos
=================================


En este apartado veremos conceptos como:

* inserción de nuevos fenómenos en un almacén

* modificación de fenómenos ya existentes en un almacén

* eliminación de fenómenos de un almacén.

* reglas de validación.



El *almacén* dispone de servicios que nos permiten
la modificación de los datos. Para poder modificar los
datos, lo primero que deberemos hacer es notificar al
*almacén* que vamos a modificar datos suyos. Y, al
igual que le notificamos que queremos modificarlos,
tendremos que informarle cuando hayamos terminado
de hacerlo. Una vez informamos al almacén que vamos a
modificar los datos, este entra en modo *modificación
de datos*, y permanecerá en ese modo hasta que le
digamos que hemos terminado.

Antes de seguir adelante vamos ha hacer un inciso sobre
el modo de operación en el que se puede encontrar un :javadoc:`FeatureStore <FeatureStore>` .
Estos puede ser:

* Modo consulta, *MODE_QUERY*. Es el modo por defecto 
  para un *almacén*. Cuando es creado uno se queda en 
  este modo. En este modo solo se pueden realizar operaciones
  de consulta, no están permitidas la ejecución de operaciones
  de edición. En caso de intentar ejecutar alguna se producirá
  una excepción :javadoc:`NeedEditingModeException <NeedEditingModeException>` .

* Modo edición, *MODE_FULLEDIT*. En este modo se permiten
  todos los comandos de edición. Se dispondrá de servicios
  de hacer/rehacer operaciones y las operaciones de edición
  no se consolidaran hasta que se termine la edición.

* Modo añadir, *MODE_APPEND*. Es un modo de edición limitado.
  No están habilitados los servicios de hacer/rehacer, y las 
  únicas acciones relacionadas con edición que están permitidas
  son las de *insert* de un nuevo fenómeno y *finishEditing*.
  La consolidación de los cambios queda a criterio del proveedor
  de datos final si se realiza cada vez que se ejecute la
  acción *insert* o al ejecutarse la acción *finishediting*.
  No todos los proveedores de datos soportan este modo. En 
  caso de que el proveedor no lo soporte y sea solicitado, 
  el *almacén* entrara automáticamente en modo *edición*.

Hay que destacar que el *Modo edición* está disponible para *todos* los
almacenes. Ahora bien, es posible de que el *almacén* no tenga
permitida la persistencia de los datos (ver método *allowWrite*) por
lo que, al finalizar la edición podemos obtener una excepción dependiendo
si podemos escribir en el *almacén* o si hemos realizado o no cambios *mayores*.

Ahora veamos cuales son las acciones, relacionadas con
edición que nos permite realizar el *almacén* de fenómenos:

* *allowWrite*. Esta acción nos informa si el *almacén*
  en cuestión es susceptible de escribir las modificaciones
  sobre el soporte de almacenamiento que tenga asociado. 

* *edit*. Esta acción es con la que le indicamos
  al almacén que queremos entrar en modo *modificación
  de datos*. Esta acción sin ningún otro parámetro mas
  entrara en modo *edición*, de otra forma habrá que indicar como
  parámetro en que modo de edición se quiere entrar.

* *cancelEditing*. Esta acción nos permite indicarle al
  *almacén* que queremos cancelar todas las modificaciones
  y pasar a modo normal o de consulta.

* *finishEditing*. Con esta acción le indicamos al almacén
  que de por terminadas las acciones de modificación, consolide
  los cambios y salga del modo de *modificación de datos*.

* *isEditing*. Nos informa si el almacén se
  encuentra en modo *modificación de datos* o
  no.

* *isAppending*. Nos informa si el almacén se
  encuentra en modo *adición de datos* o
  no.

* *undo*. Una vez estamos en modo *modificación de datos*
  y ya hemos realizado algunas modificaciones sobre el
  *almacén*, esta acción nos permite ir *deshaciendo* una
  a una las modificaciones realizadas.

* *redo*. Cuando hemos invocado al método *undo*, podemos
  volver a indicarle al *almacén* que de por valida la ultima
  acción cancelada mediante la acción *undo* invocando a
  este método. Podremos ir rehaciendo todas las acciones
  canceladas con el método *undo* invocando sucesivamente al
  método *redo*

* *insert*. Inserta un fenómeno en el almacén.

* *update*. Actualizar un fenómeno del almacén.

* *delete*. Elimina un fenómeno del almacén.

* *createNewFeature*. Al invocar a este método con un :javadoc:`FeatureType <FeatureType>` ,
  el *almacén* creara un nuevo fenómeno con la estructura
  indicada en el :javadoc:`FeatureType <FeatureType>` pasado. Además podemos indicar
  si queremos o no que se el nuevo fenómeno se rellene con los
  valores por defecto para ese tipo.

* *isAppendModeSupported*, nos informa si el *almacén*
  soporta el modo de edición *añadir nuevos fenómenos*.
  Este modo de edición presenta solo un subconjunto de las
  funciones de edición del *almacén* y esta orientado a la
  creación en modo no interactivo de nuevos juegos de datos.

* *beginEditingGroup*, *endEditingGroup*, que permiten
  agrupar un conjunto de instrucciones de edición como si
  de una sola se tratase a efectos de las acciones hacer/rehacer.
 

Además de las acciones del *almacén* que nos permiten realizar
modificaciones en los datos, conviene repasar cuales son las
acciones de los fenómenos que nos van a permitir modificar sus 
datos. En principio, un :javadoc:`Feature <Feature>` es de solo *lectura*, es decir,
solo podemos realizar operaciones de consulta sobre el. Si queremos
modificar un :javadoc:`Feature <Feature>` tendremos que obtener un :javadoc:`EditableFeature <EdtiableFeature>` a 
partir de el, el cual ya tendrá acciones asociadas a modificar sus
datos. Para obtener un :javadoc:`EditableFeature <EditableFeature>` usaremos el método *getEditable*
del :javadoc:`Feature <Feature>` . El método *createNewFeature* ya devuelve un :javadoc:`EditableFeature <EditableFeature>` .
El :javadoc:`EditableFeature <EditableFeature>` se comporta como un :javadoc:`Feature <Feature>` añadiéndole
una serie de acciones mas. Las mas importantes serian:

* *set* para permitirnos modificar los valores de los
  atributos.

* *setString, setInt, setGeometry* para permitirnos modificar los valores de los
  atributos usando tipos específicos.

* *getNotEditableCopy*, que nos devolverá una copia no modificable
  del :javadoc:`EditableFeature <EditableFeature>` .

Veamos ahora un ejemplo simple de como usar estas acciones para
insertar un nuevo fenómeno dentro el almacén. Vamos a suponer que
disponemos de un archivo *dbf* que contiene dos campos, un campo
*NOMBRE* y otro *TIPO* de tipo cadena. La apertura del almacén
se realiza tal como hemos hecho para acceder en modo lectura.
Una vez tenemos una instancia de *almacén*, el código para
añadir un nuevo fenómeno seria:

.. code-block:: java

  store = manager.createStore(parameters);
  store.edit();
  
  EditableFeature feature = store.createNewFeature();
  feature.set("NOMBRE", "Burjasot");
  feature.set("TIPO", "MUNICIPIO");

  store.insert(feature);
  store.finishEditing();

  store.dispose();

Los pasos serian los siguientes:

* comunicar al *almacén* que  vamos a entrar en
  modo *modificación de datos*. 

* Pedirle al almacén que nos suministre un fenómeno 
  vació de contenido y con la estructura de los
  fenómenos del almacén.

* Asignamos los nuevos valores a los atributos.

* Insertamos en el almacén la nueva feature.

* Y por ultimo le comunicamos al almacén que 
  hemos terminado de hacer modificaciones sobre el.

Tenemos que tener en cuenta que asta que no se ejecute la
acción *finishEditing* sobre el almacén, los cambios
que se hayan realizados no serán visibles para otros
usuarios o aplicaciones que estén accediendo al mismo
*almacén*.

Una cuestión importante a tener en cuenta relacionada con la
actualización o borrado de datos de un almacén es que pasa con
los conjuntos de datos que se han obtenido de ese almacén y sobre
los que se puede estar trabajando desde otros puntos de la aplicación.
En principio el almacén invalida todos los :javadoc:`FeatureSet <FeatureSet>` que 
se han obtenido de el tras una operación de modificación o
borrado en el almacén. De forma que tras esta operación cualquier
intento de acceder a un :javadoc:`FeatureSet <FeatureSet>` obtenido con anterioridad producirá
una excepción de tiempo de ejecución :javadoc:`ConcurrentModificationException <ConcurrentModificationException>` .
Para evitar que mientras estemos recorriendo un :javadoc:`FeatureSet <FeatureSet>` y realizando
actualización o borrados en él este quede invalidado, usaremos los
métodos *delete*, *insert* y *update* de :javadoc:`FeatureSet <FeatureSet>` o el método *remove*
del *iterador* asociado al :javadoc:`FeatureSet <FeatureSet>`.

Veamos como podríamos hacer para modificar los fenómenos
de nuestro  almacén en *dbf*. Supongamos que queremos
cambiar el valor del atributo *TIPO* para el fenómeno 
que tiene en el atributo *NOMBRE* el valor "Burjasot". 

.. code-block:: java

  store.edit();

  EditableFeature feature;
  FeatureQuery query = store.createFeatureQuery();
  query.setFilter( manager.createExpresion("NOMBRE = 'Burjasot'") );
  features = store.getFeatureSet(query);

  Iterator it = features.iterator();
  while( it.hasNext() ) {
    feature = ((Feature)it.next()).getEditable();
    feature.set("TIPO", "Municipio");
    features.update(feature);
  }
  features.dispose();

  store.finishEditing();

  store.dispose();

* Filtramos sobre el *almacén* para obtener solo los fenómenos
  que queremos. 

* Informamos al almacén que queremos realizar modificaciones sobre
  el.

* para cada fenómeno recuperado, le pedimos un :javadoc:`EditableFeature <EditableFeature>` ,
  y lo modificamos.

* después pedimos al :javadoc:`FeatureSet <FeatureSet>` que actualice el fenómeno
  en el *almacén*

* Y por ultimo le decimos al *almacén* que hemos terminado
  de realizar modificaciones.

Si quisiésemos eliminar los fenómenos que tienen como *NOMBRE*
el valor "Burjasot" seria similar a la actualización pero 
invocando al método *delete* en lugar de *update*:

.. code-block:: java

  store.edit();

  FeatureQuery query = store.createFeatureQuery();
  query.setFilter( manager.createExpresion("NOMBRE = 'Burjasot'") );
  features = store.getFeatureSet(query);

  Iterator it = features.iterator();
  while( it.hasNext() ) {
    it.next();
    it.remove();
  }
  features.dispose();

  store.finishEditing();
  store.dispose();


Relacionado con la modificación e inserción de fenómenos en un
*almacén de datos*, esta la posibilidad de asignar reglas de validación
a los fenómenos de un *almacén*. Las reglas de validación se asignan 
a cada tipo de fenómeno del almacén, es decir es el :javadoc:`FeatureType <FeatureType>`
el que controla que reglas hay que aplicar a los fenómenos de su tipo.

Podemos invocar manualmente a las reglas de validación sobre un fenómeno
mediante la acción *validate* de :javadoc:`Feature <Feature>` o sobre todos fenómenos del 
*almacén* mediante la acción *validateFeatures* del :javadoc:`FeatureStore <FeatureStore>` .

A la hora de crear una regla de validación tendremos que tener en cuenta que
una regla de validación puede ejecutarse...

* Cuando se inserte o actualice un fenómeno en el *almacén de datos*

* Al finalizar la edición se ejecutan las reglas de validación de 
  todas los fenómenos del *almacén*.

* En ambos casos.

Según nos interese tendremos que indicar cuando queremos que se ejecute
la regla de validación.

Vamos a crear una regla de validación que se encarga de que
el sentido de los segmentos de los polígonos sea el adecuado.

Para ello deberemos crear una clase que implemente el interface :javadoc:`FeatureRule <FeatureRule>` .
Este interface nos obliga a suministrar los siguientes métodos:

* *getName*, con el nombre que le queramos dar a la regla de
  validación.

* *getDescription*, opcionalmente podemos incluir una descripción de
  unas lineas asociada a la regla de validación.

* *checkAtUpdate*, devolverá el valor de cierto si la regla debe ser
  ejecutada en el momento de realizarse una modificación o inserción de un 
  nuevo fenómeno.

* *checkAtFinishEdition*, devolverá el valor cierto si la regla debe
  ejecutarse sobre todos los fenómenos del almacén al finalizar la
  edición.

* *validate*, que ejecutara la validación sobre el fenómeno que reciba
  como parámetro. En caso de que no pase la regla de validación lanzara
  una excepción.

Además de este interface, existe una clase abstract :javadoc:`AbstractFeatureRule <AbstractFeatureRule>`
que nos resuelve la gestión de cuando ha de ejecutarse, el nombre y la descripción
de la regla, dejando únicamente pendiente le implementación del método 
*validate*.


  FIXME:

  El ejemplo siguiente esta asumiendo que el validate solo
  va a ser llamado estando en modo edición. Habría que comprobar
  esto antes de invocar al *update* del store.

Veamos como quedaría nuestro ejemplo:

.. code-block:: java

  public class FeatureRulePolygon extends AbstractFeatureRule {

    public FeatureRulePolygon() {
      super("RulePolygon", "Ensure orientation of geometry");
    }

    public void validate(Feature feature, FeatureStore store)
            throws DataException {
      try {
        Geometry geom = feature.getDefaultGeometry();
        GeneralPathX gp = new GeneralPathX();
        gp.append(geom.getPathIterator(null, Converter.FLATNESS), true);

        if (gp.isClosed()) {
          if (gp.isCCW()) {
            gp.flip();
            GeometryFactory geomFactory = GeometryManager.getInstance()
                .getGeometryFactory();
            geom = geomFactory.createPolygon2D(gp);
            EditableFeature editable = feature.getEditable();
            editable.setDefaultGeometry(geom);
            store.update(editable);
          }
        }
      } catch (Exception e) {
        throw new FeatureRulePolygonException(e, store.getName());
      }
    }

    public class FeatureRulePolygonException extends DataException {
      private static final long serialVersionUID = -3014970171661713021L;
      private final static String MESSAGE_FORMAT = "Can't apply rule  in store %(store)s.";
      private final static String MESSAGE_KEY = "_FeatureRulePolygonException";

      public FeatureRulePolygonException(Throwable cause, String store) {
              super(MESSAGE_FORMAT, cause, MESSAGE_KEY, serialVersionUID);
              this.setValue("store", store);
      }
    }

  }



Podemos observar que el método *validate* recibe la feature a validar
y el *almacén* donde esta vive, y se limita a coger la geometría 
de la feature, procesarla y si es preciso actualizar el feature con
los nuevos valores.

Durante la validación de una regla podremos actualizar el fenómeno que
estamos tratando, pero no deberemos cambiar la estructura del almacén, es
decir, no deberemos realizar ninguna operación que modifique los :javadoc:`FeatureType <FeatureType>` 
de este.

Si durante el proceso de validación de fenómenos que se realiza al 
finalizar la edición alguien modifica algún fenómeno, se abortara el
proceso, quedando a decisión del que invoco a *finishEdition* comprobar
si fue por una modificación de los fenómenos mientras se procesaban para
volver a lanzar la operación.

  Nota:

  Aquí habría que poner un ejemplo de como hacer esto.
