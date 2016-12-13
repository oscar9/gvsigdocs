Acceso a objectos en gvSIG
==========================

Funciones current
-----------------

Son aquellas funciones que nos ayudan a acceder de una forma rápida a partes de gvSIG que estén en ejecución o cargadas en ese momento. Tales pueden ser como la Vista que tenemos abierta o la capa que tenemos selecciona en esa Vista.

Estas funciones se encuentran dentro de la librería ``gvsig`` y son:

.. py:function:: currentProject()

	Devuelve el proyecto actual de gvSIG
	
.. py:function:: currentDocument()

	Devuelve el documento actual (Vista o Tabla)
	
.. py:function:: currentView()

	Devuelve la Vista activa o None. Si no hay Vista activa devuelve un error de RuntimeException
	
.. py:function:: currentLayer()

	Devuelve la capa activa en la Vista

Por ejemplo, teniendo un Proyecto con una Vista abierta y una capa cargada y seleccionada en esta Vista, ejecutando::

	import gvsig

	def main(*args):

		project = gvsig.currentProject()
		print "project: ", type(project)
		print "project.name: ", project.name

		view = gvsig.currentView()
		print "view: ", type(view)
		print "view.name: ", view.name

		layer = gvsig.currentLayer()
		print "layer: ", type(layer)
		print "layer.name: ", layer.name
		
Dando como resultado en la consola algo similar a::

	Running script acceso.
	project:  <type 'org.gvsig.app.project.DefaultProject'>
	project.name:  Sin título
	view:  <type 'org.gvsig.app.project.documents.view.DefaultViewDocument'>
	view.name:  Sin título
	layer:  <type 'org.gvsig.fmap.mapcontext.layers.vectorial.FLyrVect'>
	layer.name:  points_layer-57ab8d7e99c
	Script acceso terminated.


Proyecto
--------

La clase Project ( :javadoc:`DefaultProject <DefaultProject>` ) se encarga de la gestión de los documentos que tengamos en gvSIG. Este se encarga de las Vistas, Tablas, Mapas, Gráficas y otros que pueden venir desde otras extensiones como el de Series de mapas.

Para acceder a esta clase podemos usar :py:func:`currentProject()`, nos devolverá la instancia del proyecto que se encuentra actualmente abierto en gvSIG.

Por ejemplo, podemos obtener su nombre y la proyección establecida en el proyecto::


	from gvsig import *

	def main(*args):

		project = currentProject()
		name = project.getName()
		prjcode = project.getProjectionCode()
		prj = project.getProjection()

		print "Project Name: ", name
		print "Projection Code: ", prjcode, type(prjcode)
		print "Projection: ", prj, type(prj)

Muestra por consola::

	Project Name:  Sin título
	Projection Code:  EPSG:4326 <type 'unicode'>
	Projection:  EPSG:4326 <type 'org.gvsig.crs.Crs'>
	

En este caso, `getProjection()` es un método implementado en la API de gvSIG, y `getProjectionCode()` es un método inyectado en la API de gvSIG desde las librerías de Jython desarrolladas.	

Documento Vista
---------------

El documento Vista ( :javadoc:`DefaultViewDocument <DefaultViewDocument>` ) contendrá las capas de nuestro proyecto, en el podremos visualizarlas y editarlas.

Para acceder a las Vistas creadas en un Proyecto, podremos usar dos funciones: :py:func:`currentView()` o :py:func:`currentDocument()` para acceder a la Vista activa, o  `currentProject().getView("Nombre")` para acceder a una determinada Vista::

    # encoding: utf-8

    from gvsig import *

    def main(*args):

        project = currentProject()
        
        # Acceso a vista con nombre "Vista1"
        view1 = currentProject().getView("Vista1")
        

Una Vista pueden contener diferentes capas o servicios cargados. Un Proyecto puede contener varias vistas. Por ejemplo, con el siguiente script listaremos todas las Vistas que se encuentren en nuestro proyecto::

	from gvsig import *

	def main(*args):

		project = currentProject()
		views = project().getViews()
		for view in views:
			print view

También podemos realizar otras operaciones. Por ejemplo, podemos crear una Vista nueva en nuestro Proyecto y cambiarle la proyección que tiene::

	from gvsig import *

	def main(*args):

		project = currentProject()
		
		# Creamos nueva vista
		view = project.createView("Nueva Vista")
		print "Vista nueva: ", view.getName()
		print "Proyeccion de la Vista: ", view.getProjectionCode()

		# Nos ayudamos de una funcion para obtener el crs object
		# correspondiente a un codigo crs
		newcrs = getCRS("EPSG:32630")
		view.setProjection(newcrs)
		
		print "Nueva proyeccion de la Vista: ", view.getProjectionCode()
		
En el caso de que ya exista una Vista con ese nombre, se añadirá a él un indice. Si volvemos a ejecutar el script anterior, el nombre de la nueva vista será: "Nueva Vista - 1".

Si tenemos la Vista anteriormente creada abierta en gvSIG, podemos acceder directamente a esa Vista abierta cuando ejecutamos nuestro script mediante :py:func:`currentView()`. Por ejemplo::


	from gvsig import *

	def main(*args):

		view = currentView()
		print "Nombre de la Vista: ", view.getName()

		
Podemos centrar la vista en el punto que queramos::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		view = currentView()
		encuadre = createEnvelope([10,10],[20,20])
		view.getMapContext().getViewPort().setEnvelope(encuadre)

		view.centerView(createEnvelope([20,20],[50,50]))
	
	
Documento Tabla
----------------
Otro tipo de documentos que tenemos en nuestro proyecto son las Tablas ( :javadoc:`DefaultFeatureStore <DefaultFeatureStore>` ). Estas tablas pueden hacer referencia tanto a tablas añadidas a gvSIG como a tablas de atributos de las capas u otras que aparezcan en este gestor.

De la misma forma que los documentos vista, podemos utilizar la función :py:func:`currentTable()` o :py:func:`currentDocument()` o `project.getTable("Name")`.


Capas
-----

Cualquier capa o servicio añadido a nuestras Vistas serán accesibles mediante Scripting. 

Por ejemplo, una operación básica en herramientas que creemos, sería la creación de una **capa vectorial** y añadirla a una Vista nueva::


	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		# Creamos esquema para la capa
		ft = createFeatureType()
		ft.append("GEOMETRY", "GEOMETRY")

		# Establecemos el tipo del geometria.
		# Usamos constantes POINT y D2 que se encuentran
		# dentro de la libreria gvsig.geom
		ft.get("GEOMETRY").setGeometryType(POINT, D2)

		# Creamos la capa nueva con el nuevo esquema
		# La funcion se encarga de establecer un path temporal
		shp = createShape(ft)

		# Agregamos la capa a nuesta vista nueva
		# Se creara con el nombre de View 001 por defecto
		newview = currentProject().createView()
		newview.addLayer(shp)

Tenemos la opción de iterar sobre todas las capas que tenga una Vista::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):
		view = currentView()
		layers = view.getLayers()

		# Acceder iterando las capas
		print "\nIterando: "
		for layer in layers:
			print "\tCapa: ", layer.getName(), 
			print " Tipo: ", layer.getTypeVectorLayer().getFullName()

		# Acceder mediante indices
		print "\nMediante indices: "
		for i in range(0, len(layers)):
			print "\tCapa: ", layers[i].getName(),
			print " Tipo: ", layers[i].getTypeVectorLayer().getFullName()

Si tenemos una Vista con tres capas, el resultado por consola será similar al siguiente::

	Iterando: 
		Capa:  tmpshp-57ae45dd1765  Tipo:  Point:2D
		Capa:  tmpshp-57ae45f712b6  Tipo:  MultiCurve:2D
		Capa:  tmpshp-57ae45fe1112  Tipo:  MultiSurface:3DM

	Mediante indices: 
		Capa:  tmpshp-57ae45dd1765  Tipo:  Point:2D
		Capa:  tmpshp-57ae45f712b6  Tipo:  MultiCurve:2D
		Capa:  tmpshp-57ae45fe1112  Tipo:  MultiSurface:3DM
	
Si quieres acceder a capas ya existentes en la Vista podrías hacerlo mediante ``currentView().getLayer("Nombre")``, si la tenemos seleccionada en la tabla de contenidos (TOC) mediante :py:func:`currentLayer()`
	
Otros métodos que podemos usar sobre una capa añadida son los de ``.setVisible(True)`` para modificar su visibilidad en la Vista, o `` layer.setActive(True)`` para modificar su selección dentro de la Tabla de Contenidos.

Grupo de entidades: FeatureSet
------------------------------

Para obtener las entidades de una capa o tabla, podemos hacer una petición mediante ``layer.features()``, la cual hace una petición al **store** de la capa, y nos devuelve un featureSet ( :javadoc:`DefaultFeatureSet <DefaultFeatureSet>` ) con el filtrado o orden que le asignemos. Este featureSet nos permite iterar sobre las entidades de la capa.

Después, por ejemplo, podemos acceder a estas entidades y a sus valores mediante el método ``getValues()`` sobre cada ``feature``, el cual devuelve un diccionario que podemos imprimir::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):
		layer = currentLayer()
		features = layer.features()
		print "Numero entidades: ", features.getSize()

		for feature in features:
			print feature.getValues()
		
Por consola el resultado en este caso sería::

	Numero entidades:  7
	{u'NAME': u'Feature1', u'ID': 1L, u'GEOMETRY': POINT (1.0 2.0)}
	{u'NAME': u'Feature2', u'ID': 2L, u'GEOMETRY': POINT (5.0 3.0)}
	{u'NAME': u'Feature2', u'ID': 3L, u'GEOMETRY': POINT (3.0 3.0)}
	{u'NAME': u'Feature2', u'ID': 4L, u'GEOMETRY': POINT (2.0 1.0)}
	{u'NAME': u'Feature3', u'ID': 5L, u'GEOMETRY': POINT (2.0 6.0)}
	{u'NAME': u'Feature3', u'ID': 6L, u'GEOMETRY': POINT (6.0 2.0)}
	{u'NAME': u'Feature3', u'ID': 7L, u'GEOMETRY': POINT (2.0 7.0)}


Podemos hacer diferentes filtrados que devolverían diferentes featureSet. El parámetro ``expresion`` pide un String que haga de filtro, el parámetro ``sortBy`` el campo sobre el que se ordenará el featureSet, el parámetro ``asc`` ordenará en orden ascendente o descendente según el campo seleccionado.

Unos ejemplos de filtrados y sus resultados::

	features = layer.features(expresion="ID < 4", sortby="NAME", asc=True)		

	{u'NAME': u'Feature1', u'ID': 1L, u'GEOMETRY': POINT (1.0 2.0)}
	{u'NAME': u'Feature2', u'ID': 2L, u'GEOMETRY': POINT (5.0 3.0)}
	{u'NAME': u'Feature2', u'ID': 3L, u'GEOMETRY': POINT (3.0 3.0)}

	features = layer.features(expresion="ID < 4 AND 1 < ID", sortby="NAME", asc=False)
	
	{u'NAME': u'Feature2', u'ID': 2L, u'GEOMETRY': POINT (5.0 3.0)}
	{u'NAME': u'Feature2', u'ID': 3L, u'GEOMETRY': POINT (3.0 3.0)}

    features = layer.features(expresion="ID < 4 AND NAME != 'Feature1'", asc=True)

	{u'NAME': u'Feature2', u'ID': 2L, u'GEOMETRY': POINT (5.0 3.0)}
	{u'NAME': u'Feature2', u'ID': 3L, u'GEOMETRY': POINT (3.0 3.0)}
	
Otras opciones más avanzadas usando la API de gvSIG::

	from gvsig import *

	def main(*args):

		layer = currentLayer() #layer_append_features.main()
		features = layer.features()

		print "\n Show features: "
		for f in features:
			print f

		fquery = layer.getDataStore().createFeatureQuery()
		fquery.addAttributeName("ID")
		fquery.addAttributeName("GEOMETRY")

		#FeatureQuery
		print "\nFQuery"
		#fquery.setLimit(3)
		fset = layer.getDataStore().getFeatureSet(fquery)
		for i in fset:
			print i

		#FeatureQueryOrder
		print "\n Fquery order geometry"
		forder = layer.getDataStore().createFeatureQuery().getOrder()
		print forder
		forder.add("GEOMETRY", True)
		fquery.setOrder(forder)

		fsetorder = layer.getDataStore().getFeatureSet(fquery)

		for i in fsetorder:
			print i

			
Entidad
-------

Antes hemos visto que podemos acceder a los valores de cada entidad ( :javadoc:`DefaultFeature <DefaultFeature>` ) con ``getValues()``, pero también podemos acceder directamente mediante ``feature.FIELD`` o feature.get("Field"). Por ejemplo::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):
		layer = currentLayer()
		features = layer.features()
		for feature in features:
			print "ID: ", feature.ID, " NAME: ", feature.get("NAME")
			
La salida por consola será::

	ID:  1  NAME:  Feature1
	ID:  2  NAME:  Feature2
	ID:  3  NAME:  Feature2
	ID:  4  NAME:  Feature2
	ID:  5  NAME:  Feature3
	ID:  6  NAME:  Feature3
	ID:  7  NAME:  Feature3

	
Selección
+++++++++

Otro tipo de featureSet es el :javadoc:`DefaultFeatureSelection <DefaultFeatureSelection>`. Hace referencia a los objetos que tengamos seleccionados en la capa.

Un ejemplo de su uso, teniendo 3 entidades seleccionadas::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):
		layer = currentLayer()
		#features = layer.features()
		selection = layer.getSelection()

		for feature in selection:
			print "ID: ", feature.ID, " NAME: ", feature.get("NAME")

Dando como resultado::

	ID:  1  NAME:  Feature1
	ID:  2  NAME:  Feature2
	ID:  3  NAME:  Feature2

Disponemos de dos métodos especiales para esta clase como son el ``.selectAll()`` para seleccionar todas las entidades de esa capa o ``.deselectAll()`` para deseleccionar todos ellos.

Por ejemplo, añadiremos a la selección ciertas entidades que cumplan un criterio::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		layer = currentLayer()

		#Entidades de la capa
		features = layer.features()

		#Seleccion de entidades 
		selection = layer.getSelection()
		selection.deselectAll()
		
		for feature in features:
			if feature.ID < 3:
				# Agregamos entidades a la seleccion
				selection.select(feature)

Si quisiéramos eliminar entidades de la selección, podríamos usar el método ``.deselect(feature)``

Por otra parte, también podríamos crear una selección o varias desde Scripting::

	from gvsig import *

	def main(*args):

		# Create a new selection
		layer = currentLayer()
		features = layer.features()
		newselection = layer.getDataStore().createSelection()
		
		for f in features:
			if f.ID!=10:
				newselection.select(f)

		layer.getDataStore().setSelection(newselection)
