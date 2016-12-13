Bloqueo de fenómenos
====================

 Nota:

  En relación a la gestión de bloqueos están definidos los interfaces
  que forman parte del API pero no se ha realizado aun ninguna implementación
  de estos.Es posible que sufra algunas variaciones
  tras su implementación.


En ocasiones puede ser interesante disponer de funciones de bloqueo de 
fenómenos sobre un almacén para evitar que desde otros puestos modifiquen
los datos sobre los que vamos a trabajar en un momento dado. Para dar 
servicio a esta necesidad, el :javadoc:`FeatureStore <FeatureStore>` dispone de dos métodos:

* *isLocksSupported*, que nos informa de si ese almacén soporta
  gestión de bloqueos.

* *getLocks*, que actúa a modo de conjunto de fenómenos que tengo
  bloqueados en un momento dado en el almacén. En caso de que el
  almacén no soporte bloqueos, este método devolverá *null*.

Cuando un almacén soporta la gestión de bloqueos, el método *getLocks* nos
devolverá un :javadoc:`FeatureLocks <FeatureLocks>`. Esta clase se encargara de llevar la cuenta de
los bloqueos que se establecen así como de su tiempo de vigencia. Los bloqueos 
se establecen en grupos de fenómenos, y a cada grupo de fenómenos que se
bloquea se le asigna un tiempo de vigencia para ese bloqueo. Para bloquear 
fenómenos tendremos los métodos:

* *lock* y *lockAll*, que añade un fenómeno o conjunto de estos al 
  grupo de fenómenos a bloquear.

* *unlock* y *unlockAll*, que elimina un fenómeno o conjunto de estos del
  grupo de fenómenos a bloquear.

* *setDefaultTimeout* y *getDefaultTimeout*, que permiten fijar el tiempo
  de vigencia de los bloqueos.

* *apply*, que crea un grupo de bloqueos con los bloqueos indicados mediante 
  las operaciones *lock* con el tiempo de vigencia por defecto y aplica los
  bloqueos en el almacén.

Además dispondremos de operaciones para consultar si un fenómeno esta
bloqueado, así como para consultar los grupos de bloqueos que se han
establecido.

* *getGroups*, que nos devuelve un iterador sobre los grupos
  de bloqueos establecidos. Cada grupo de bloqueos esta 
  representado por la clase :javadoc:`FeatureLocksGroup <FeatureLocksGroup>` .

* *getGroupsCount*, que nos dice cuantos grupos de bloqueos 
  hay.

* *isLocked*, que nos informa sobre si un fenómeno esta bloqueado
  independientemente de en que grupo de bloqueo se bloqueo.

* *getLocksCount*, que nos informa de cuantos fenómenos tenemos
  bloqueados.

* *getLocks*, que nos devuelve un iterador sobre todos los
  fenómenos bloqueados.

También es interesante tener en cuenta que :javadoc:`FeatureLocks <FeatureLocks>` implementa
:javadoc:`Observable <Observable>` , de forma que podemos registrarnos a cambios en el. Los
cambios que podemos observar son:

* Se ha aplicado un nuevo grupo de bloqueos.

* Un grupo de bloqueos a caducado.

* Un grupo de bloqueos se ha liberado.

Los principales métodos u operaciones que podemos encontrar en un
grupo de bloqueos, :javadoc:`FeatureLocksGroup <FeatureLocksGroup>`, son:

* *getTimeout*, tiempo de caducidad asignado al bloqueo.

* *XXX*, que nos devuelve el tiempo en milisegundos que resta
  asta que el bloqueo caduque.

* *unlock*, libera todos los bloqueos del grupo, haciendo
  desaparecer a este.

* *iterator*, que nos devuelve los fenómenos, :javadoc:`Feature <Feature>`, que se
  encuentran bloqueados en ese grupo.

* *getLocksCount*, que nos informa sobre cuantos fenómenos hay
  bloqueados en ese grupo de bloqueos.

* *isLocked*, que nos informa si un fenómeno esta bloqueado 
  en este grupo de bloqueos.
  
  
