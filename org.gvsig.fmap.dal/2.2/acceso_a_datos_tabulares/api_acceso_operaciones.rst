 
Acceso a operaciones específicas del almacén
============================================

.. _libTools org.gvsig.tools: http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.fmap.dal/2.0.0/featurestore/consumer-services/org.gvsig.tools
.. _libtools - DynObject: http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/org.gvsig.fmap.dal/2.0.0/featurestore/consumer-services/org.gvsig.tools/org.gvsig.tools.dynobject

Hasta ahora hemos estado viendo una serie de servicios que un almacén de datos, 
a través del :javadoc:`FeatureStore <FeatureStore>` ofrece sin importarnos qué tipo de almacén de datos
sea. Son servicios que ofrecen de forma *estándar* todos los almacenes de datos
tabulares. Sin embargo, podemos encontrarnos que para algún tipo especial de
almacén de datos este ofrezca algún servicio extra, bien porque el propio 
proveedor de datos lo suministra, bien porque alguna librería externa suministra
ese servicio y lo ofrece a través del propio almacén.

Cuando nos referimos a *operaciones*, nos referimos a estos servicios extra que
pueden estar asociados a un almacén especifico.

Para acceder a estas operaciones lo hacemos a través de la utilidad :javadoc:`DynObject <DynObject>` que
presenta la librería `libTools org.gvsig.tools`_ . La clase :javadoc:`FeatureStore` implementa el interface :javadoc:`DynObject <DynObject>`
usando el nombre de clase dinámica *FeatureStore*. Llegados a este punto es recomendable 
que repase la documentación de esta utilidad antes de seguir (`libtools - DynObject`_), 
aunque si no lo hace
intentaremos dar las descripciones necesarias para que pueda entender el uso
que se hace de ella desde el :javadoc:`FeatureStore <FeatureStore>`.

El :javadoc:`FeatureStore <FeatureStore>` por implementar el interface :javadoc:`DynObject <DynObject>` dispone de un método, *getDynClass*,
que nos devolverá un objeto :javadoc:`DynClass <DynClass>`. Este contiene una definición de métodos y atributos
añadidos dinámicamente a la instancia de nuestro almacén. Podemos usar ese :javadoc:`DynClass <DynClass>` para
interrogar por los métodos añadidos de la siguiente forma:

.. code-block:: java

    DynMethod[] methods = store.getDynClass().getDynMethods();
    for( int i=0; i<methods.length; i++ ) {
    System.out.print("method '"+ methods[i].getName() + "'");
    if( methods[i].getDescription() != null ) {
        System.out.print(", "+ methods[i].getDescription());
    }
    System.out.println(".");
    }
    
Con este ejemplo obtendremos un listado de métodos u operaciones que han sido añadidos a nuestro almacén, con su nombre y su descripción si la tuviese.

¿ Y cómo invocamos a esas operaciones o métodos ?

Supongamos que sabemos que hay almacenes que pueden suministrar una leyenda específica para sus datos mediante una operación getLegend. Podemos utilizar el siguiente código para invocar a esa operación y obtener la leyenda en caso de que nuestro almacén lo soporte.

.. code-block:: java

    if( store.getDynClass().getDynMethod("getLegend")!= null ) {
    ILegend Legend = (ILegend)store.invokeDynMethod("getLegend", null);
    }
    
De cara al uso de las operaciones especificas que puede presentar un almacén de datos, estas son las principales operaciones que vamos a tener que realizar:

* consultar de que operaciones disponemos.
* invocar a alguna de esas operaciones.

Si queremos saber más sobre cómo crear sus propias operaciones consulte el apartado Servicios asociados al proveedor de datos, SPI.