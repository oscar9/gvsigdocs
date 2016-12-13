Trabajando con capas: FLayer
============================

Trabajando con capas :javadoc:`FLayer <FLayer>`


.. py:function:: createShape(definition, [filename=None, geometryType=None, CRS=None, prefixname="tmpshp"])

    Creates a new shape layer. If geometryType is None, will take geometry type from the definition. If parameter geometryType and the geometry type inside the definition are different, raises an error.

    :param definition: Layer schema
	:type definition: DefaultFeatureType
    :param str filename: Full path
    :param geometryType: 
	:type geometryType: GeometryType
    :param str CRS: CRS Code
	:param str prefixname: first part of the temp name

La interfaz FLayer viene implementada en varias capas de uso común como son las capas vectoriales o raster.

Capas vectoriales: FLyrVect
---------------------------

Unas de las operaciones más importantes es el trabajo con capas vectoriales :javadoc:`FLyrVect <FLyrVect>`. Hemos querido simplificar al máximo posible el trabajo con ellas, sobretodo si eres un nuevo desarrollador.

Ya hemos visto anteriormente la generación de esquemas que pueden ser usados en capas vectoriales. La única condición de estos esquemas es que contengan un campo ``GEOMETRY`` con un tipo de geometría establecido.

Crear capa vectorial
++++++++++++++++++++

Para la generación de capas hemos simplificado al máximo la función :py:func:``createShape(definition)``. El único parámetro obligatorio será el esquema de la capa con su campo geometría.

Por ejemplo, lo mínimo necesario para crear una capa es::


	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("GEOMETRY", "GEOMETRY")
		schema.get("GEOMETRY").setGeometryType(POINT, D2)
		shape = createShape(schema)
                        
De esta forma, si la ruta o el nombre del fichero no es importante para nosotros, algo muy común a la hora de generar capas para resultados de geoprocesos o similares, la función se encargará de asignarle una ruta en una carpeta temporal y un nombre aleatorio (basado en códigos de tiempo).

Otra forma sería usar directamente la funcion `createLayer()`::

        layer = createLayer(schema=schema,
                            servertype="FilesystemExplorer",
                            layertype="Shape",
                            shpFile="/home/osc/temp/test1.shp",
                            CRS="EPSG:25830",
                            geometryType=geom.POINT
                            )

Así en el script anterior podemos añadir las líneas::

    print "Nombre: ", shape.getName()
    print "Ruta: ", shape.getDataStore().getFullName()
	
Que darán como resultado el nombre y ruta de la capa, similar a::

	Nombre:  tmpshp-57afa1381035
	Ruta:  C:\Users\Oscar\AppData\Local\Temp\tmp-andami\tmpshp-57afa1381035.shp
	
Si necesitas crear varias capas y necesitas poder diferenciarlas, por ejemplo, al enlazar una serie de operaciones, podemos establecer el parámetro ``prefixname`` el cual modificará la primera parte del nombre, pero seguirá creando todo el resto de ruta temporal y asegurándonos que será un nombre único::


	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("GEOMETRY", "GEOMETRY")
		schema.get("GEOMETRY").setGeometryType(POINT, D2)
		shape1 = createShape(schema, prefixname="resultado")
		shape2 = createShape(schema, prefixname="errores")
		shape3 = createShape(schema, prefixname="puntos")
		
		print "Nombre: ", shape1.getName(), "\tRuta: ", shape1.getDataStore().getFullName() 
		print "Nombre: ", shape2.getName(), "\tRuta: ", shape2.getDataStore().getFullName()
		print "Nombre: ", shape3.getName(), "\tRuta: ", shape3.getDataStore().getFullName()

Por consola muestra el nombre de las capas como indicábamos::

	Nombre:  resultado-57afa4f612c9 	Ruta:  C:\Users\Oscar\AppData\Local\Temp\tmp-andami\resultado-57afa4f612c9.shp
	Nombre:  errores-57afa4f613a6 	Ruta:  C:\Users\Oscar\AppData\Local\Temp\tmp-andami\errores-57afa4f613a6.shp
	Nombre:  puntos-57afa4f61446 	Ruta:  C:\Users\Oscar\AppData\Local\Temp\tmp-andami\puntos-57afa4f61446.shp
	
Modificar esquema de una capa
+++++++++++++++++++++++++++++

El siguiente script modificará el esquema de una capa. Para ello tenemos que crear un esquema nuevo basado en el anterior de la capa mediante ``createFeatureType(layer_schema)``, realizar las modificaciones y actualizar la capa::

	from gvsig import *
	from gvsig import geom

	def main(*args):
		"""Updating schema of existent layer"""
		
		layer = currentLayer()
		
		schema = layer.getSchema()
		newschema = createSchema(schema)
		newschema.append("ID2", "STRING")
		
		layer.edit()
		layer.update(newschema)
		layer.commit()

Operaciones con entidades
+++++++++++++++++++++++++

Una vez creada la capa nueva o accedido a una de ellas con ``currentLayer()`` o ``view.getLayer("Name")``, podemos acceder a sus entidades mediante el método ``.features()``, tal y como explicamos en la guía de Acceso a datos.

Lo siguiente que haremos es añadir datos a esta capa vectorial. Para ello ponemos la capa en modo de edición mediante ``layer.edit()`` y agregamos las entidades con ``layer.append(args)``::


	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("ID", "INTEGER", 5)
		schema.append("NAME", "STRING", 10)
		schema.append("GEOMETRY", "GEOMETRY")
		schema.get("GEOMETRY").setGeometryType(POINT, D2)
		
		shape = createShape(schema, prefixname="resultado")

		
		print "Nombre: ", shape.getName(), "\tRuta: ", shape.getDataStore().getFullName()

		shape.edit()
		# Setting arguments
		shape.append(ID=1, NAME="Valencia", GEOMETRY=createPoint2D(10, 10))
		# Diccionary
		shape.append({"ID": 2, "NAME": "Paris", "GEOMETRY":createPoint2D(15, 15)})
		shape.commit()

		currentView().addLayer(shape)


Otro ejemplo añadiendo entidades, usando también la forma con la que se haría desde Java::

	import gvsig
	reload(gvsig)
	from gvsig import *
	from gvsig import geom

	from org.gvsig.fmap.dal.feature import FeatureStore
	def main(*args):

		# Creating new layer
		schema = createSchema()
		schema.append("ID", "INTEGER")
		schema.append("NAME", "STRING", 10)
		schema.append("GEOMETRY", "GEOMETRY")
		schema.get('GEOMETRY').setGeometryType(geom.POINT,geom.D2)
		
		layer = createShape(schema, prefixname="points_layer")

		# Insert with newfeature
		store = layer.getFeatureStore()
		
		newfeature = store.createNewFeature()
		newfeature.set("ID",1)
		newfeature.set("NAME","Feature1")
		newfeature.set("GEOMETRY", geom.createPoint(geom.D2, 1,2))
		
		layer.edit(FeatureStore.MODE_APPEND) #solo para capas recien creadas
		store.insert(newfeature)
		layer.commit()

		# Insert with append
		layer.edit()
		layer.append(ID=2,NAME='Feature2',GEOMETRY=geom.createPoint(geom.D2, 5, 3))
		
		layer.append({'ID':3,'NAME':'Feature2','GEOMETRY':geom.createPoint(geom.D2, 3, 3)})
		layer.append({'ID':4,'NAME':'Feature2','GEOMETRY':geom.createPoint(geom.D2, 2, 1)})
		layer.append({'ID':5,'NAME':'Feature3','GEOMETRY':geom.createPoint(geom.D2, 2, 6)})
		layer.append({'ID':6,'NAME':'Feature3','GEOMETRY':geom.createPoint(geom.D2, 6, 2)})
		layer.append({'ID':7,'NAME':'Feature3','GEOMETRY':geom.createPoint(geom.D2, 2, 7)})
		layer.commit()

		# Add layer to the view
		currentView().addLayer(layer)

		print "Features info"
		for l in layer.features():
			print l

		
		
Si al final del script anterior añadimos las siguientes líneas, veremos un ejemplo para eliminar entidades::


		features = layer.features("ID < 6") #DefaultFeatureSet
		
		layer.edit()
		print type(layer)
		print features, type(features)
		for i in features:
			features.delete(i)

		layer.commit()

	
Para modificar los valores de las entidades que contiene nuestra capa::

		layer.edit()

		for i in features:
			print i
			c = i.getEditable()
			c.set("NAME", "Modified_4")
			features.update(c)
			
		layer.commit()
		

Se puede realizar copias de entidades (features) y poder modificarlas después en su capa original.

Ejemplo: Extraemos ciertas entidades de una capa que contiene un Campo1 de tipo Long. Estas entidades las copiamos a una lista. Después, modificamos estas entidades y las volvemos a modificar sobre la capa inicial::

    from gvsig import *

    def main(*args):
        layer = currentLayer()
        features = layer.features('Campo1>2',sortBy="Campo1",asc=True)
        lista = []
        for f in features:
            print f
            copia = f.getCopy()
            print type(copia)
            lista.append(copia)

        print len(lista)
        layer.edit()
        for i in lista:
            value = i.get('Campo1')+0.01
            i = i.getEditable()
            i.set('Campo1', value)
            print "new value", i.get('Campo1'), type(i)
            featureSet = layer.features()
            layer.features().update(i)
        layer.commit()
