 
La selección dentro de los datos de un almacén
==============================================

Sobre la selección de datos de un almacén, en este apartado se trata:

- Cómo acceder a la selección de un almacén.
- Cómo cambiar la selección de un almacén por otra.
- Operaciones que se pueden realizar con la selección.
- Recepción de eventos de selección.

El acceso a la selección de un almacén se realiza a través del método *getSelection* del almacén:

.. code-block:: java

    FeatureSelection selection = store.getFeatureSelection();

Si no queremos trabajar directamente con la selección del almacén, podemos crear una selección nueva a través del propio almacén:

.. code-block:: java

    FeatureSelection selection2 = store.createFeatureSelection();

Si queremos sustituir la selección del almacén por otra bastará con establecerla:

.. code-block:: java

    store.setSelection(selection2);

Una vez obtenida la selección, a través de ella podemos realizar las siguientes operaciones:

- Seleccionar o de seleccionar un fenómeno, un :javadoc:`FeatureSet <FeatureSet>` o todos los fenómenos del almacén.

- Averiguar si un fenómeno está seleccionado.

- Invertir la selección.

- Obtener los fenómenos seleccionados o de seleccionados, ya que el :javadoc:`FeatureSelection <FeatureSelection>` es, a su vez, un :javadoc:`FeatureSet <FeatureSet>`.

Ej:

.. code-block:: java

    Feature feature = ...

    // Seleccionamos un fenómeno
    selection.select(feature);

    // Devolverá true
    selection.isSelected(feature);

    // Invertimos la selección
    selection.reverse();

    // Devolverá false
    selection.isSelected(feature);

    // Seleccionamos todos
    selection.selectAll();

    // Devolverá true
    selection.isSelected(feature);

    // Deseleccionamos el fenómeno
    selection.deselect(feature);

    // Devolverá false
    selection.isSelected(feature);


Si lo que queremos es recibir eventos sobre cambios en la selección, podemos suscribirnos como :javadoc:`Observer <Observer>` al almacén directamente, de forma que recibiremos, entre otros eventos, el evento :javadoc:`DataStoreNotification.SELECTION_CHANGE <DataStoreNotification>` .SELECTION_CHANGE. 

Por ejemplo, la tabla que muestra una lista de fenómenos, debe repintarse para actualizar las filas seleccionadas cuando cambia la selección en el almacén correspondiente. Para ello implementa :javadoc:`Observer <Observer>` y se registra como tal en el almacén. Su método *update* es como sigue:

.. code-block:: java

    public void update(Observable observable, Object notification) {
        if (notification instanceof FeatureStoreNotification) {
            FeatureStoreNotification fsNotification = (FeatureStoreNotification) notification;
            String type = fsNotification.getType();
            // If the selection has changed, repaint the table to show the new
            // selected rows
            if (FeatureStoreNotification.SELECTION_CHANGE.equals(type)) {
                repaint();
            }
        }
    }



