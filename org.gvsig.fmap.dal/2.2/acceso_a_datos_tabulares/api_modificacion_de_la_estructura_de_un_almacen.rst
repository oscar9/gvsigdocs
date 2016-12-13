 
Modificación de la estructura de un almacén
===========================================
   
.. _consultando la estructura de un almacén: api_consultando_la_estructura_de_un_almacen.html
.. _creación de un nuevo almacén: api_creacion_de_un_nuevo_almacen.html

En el apartado `consultando la estructura de un almacén`_ vimos como podemos consultar 
la estructura de un almacén de datos. Esta venia descrita por el :javadoc:`FeatureType <FeatureType>` y el 
:javadoc:`FeatureAttributeDescriptor <FeatureAttributeDescriptor>`. En el apartado `creación de un nuevo almacén`_ vimos como 
podemos crear un nuevo almacén de datos y los equivalentes al :javadoc:`FeatureType <FeatureType>` y 
:javadoc:`FeatureAttributeDescriptor <FeatureAttributeDescriptor>` que nos permiten modificarlos, el :javadoc:`EditableFeatureType <EditableFeatureType>` y
el :javadoc:`EditableFeatureAttributeDescriptor <EditableFeatureAttributeDescriptor>` . En general estas dos últimos interfaces
contienen todo lo que podemos necesitar no solo para crear nuevos almacenes de datos
sino para modificar los ya existentes.

Igual que para modificar un fenómeno de un almacén, para modificar la estructura de
estos tenemos que estar en modo edición. Luego solo tenemos que limitarnos a obtener el
:javadoc:`FeatureType <FeatureType>` que queremos modificar; cambiar, añadir o borrar atributos sobre el y actualizar
el almacén con los cambios mediante el método *update*.

Veamos esto con un simple ejemplo.
En `creación de un nuevo almacén`_ creamos un almacén de datos con dos campos, *MUNICIPIO*
y *NOMBRE* de tipo cadena y un tamaño de 100 caracteres. Vamos a cambiar el tamaño de 
estos atributos a 50 caracteres.

.. code-block:: java

  store.edit();
  FeatureType featureType = store.getDefaultFeatureType();
  EditableFeatureType editableFeatureType = featureType.getEditable();
  editableFeatureType.getAttributeDescriptor("MUNICIPIO").setSize(50);
  editableFeatureType.getAttributeDescriptor("NOMBRE").setSize(50);
  store.update(editableFeatureType);
  store.finishEdition()


Los pasos realizados son:

#. Iniciar edición.

#. Obtener el :javadoc:`FeatureType <FeatureType>` asociado al almacén.

#. Obtener una versión editable del :javadoc:`FeatureType <FeatureType>`.

#. Acceder a los descriptores de los atributos y cambiarles 
   el *size* a 50.

#. Actualizar el almacén con la nueva definición. A partir de este
   momento, las acciones sobre el almacén tendrán en cuenta la nueva
   definición, aunque los cambios no se han consolidado en disco.

#. Por ultimo, terminaremos la edición, quedando consolidados en 
   disco los cambios de estructura del almacén.


