 
La pila de comandos de edición
==============================

Cuando un almacén se pone en edición, internamente genera una pila de comandos con las operaciones que se van realizando. De esta forma, dichas operaciones se podrán deshacer o rehacer.

Para ello el almacén implementa el interfaz :javadoc:`UndoRedoStack <UndoRedoStack>` . Dicho interfaz aporta operaciones para:

- *undo:* deshacer la última o últimas operaciones realizadas.

.. code-block:: java

    Feature feature = store.createNewFeature();
    feature.setAttribute("NOMBRE", "Burjasot");
    feature.setAttribute("TIPO", "MUNICIPIO");

    store.insert(feature);

    // Se deshace la inserción de la feature anterior
    store.undo();


- *redo:* volver a rehacer la última o últimas operaciones que han sido deshechas.

.. code-block:: java

   // Se vuelve a insertar la feature
   store.redo();

- Ver si se puede seguir deshaciendo o rehaciendo operaciones.

.. code-block:: java

   if (store.canUndo()) {
        System.out.println("Hay operaciones que se pueden deshacer");
   }

- Obtener información acerca de la pila de operaciones que se pueden deshacer o rehacer.

De cada operación de la pila podemos obtener la siguiente información a través de la clase :javadoc:`UndoRedoInfo <UndoRedoInfo>`:

- Una descripción de la operación.

- Fecha en la que se realizó la operación.

- El tipo de operación realizada, desde el punto de vista de cambios en los datos: inserción, modificación o borrado.

.. code-block:: java

   List infos = store.getRedoInfos();
   for (int i = 0; i < infos.size(); i++) {
        UndoRedoInfo info = (UndoRedoInfo)infos.get(i);
        System.out.println("Operación " + i + ": " + info.getDescription());
   }

En un almacén, algunas de las operaciones que se incorporarán a la pila en el modo de edición son:

- Inserción, modificación o borrado de fenómenos.

- Cambios en la estructura de los fenómenos.

- Cambios en la selección del almacén.

Hay que tener en cuenta que, una vez el almacén deja de estar en edición, la pila de comandos se descarta y los métodos del interfaz :javadoc:`UndoRedoStack <UndoRedoStack>` dejan de ser funcionales. Si volvemos a poner el almacén en edición, se creará una pila de comandos nueva.
