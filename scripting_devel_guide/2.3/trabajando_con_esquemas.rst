Trabajando con esquemas: FeatureType
====================================

Los descriptores contienen la información sobre el nombre, tipo, precisión, etc de los campos en nuestros contenedores de datos.

Según sea su uso tendrán que cumplir ciertos requisitos. Por ejemplo, si creamos una capa vectorial, su esquema tiene que tener obligatoriamente un campo **GEOMETRY** establecido.

Creando esquemas
----------------

.. py:function:: createFeatureType([schema=None]):
    
    Create an empty schema. If parameter schema is a :javadoc:`DefaultEditableFeatureType <DefaultEditableFeatureType>`, creates a copy of this schema :javadoc:`DefaultFeatureType <DefaultFeatureType>`.
	
    :param schema: Schema from other layer
    :type schema: :javadoc:`DefaultEditableFeatureType <DefaultEditableFeatureType>`
    :return: empty schema or a copy of a schema
    :type return: DefaultEditableFeatureType
	
La interfaz :javadoc:`DataTypes <DataTypes>` especifica un conjunto de constantes indicando los tipos de datos soportados por DAL.

Vamos a ver como crear un esquema con dos campos y asignarle este esquema a una nueva tabla::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("ID", "INTEGER", 10)
		schema.append("NAME", "STRING", 20)

		dbf = createDBF(schema)
		
		d = loadDBF( dbf.getFullName()) # Carga tabla en el proyecto
		
También podemos crear un esquema nuevo basado en otro ya existente, muy útil para cuando queramos hacer copias de capas. Para pasar un esquema para la creación de capas debe ser de tipo **DefaultFeatureType**, la función :py:func:``createFeatureType()`` convierte el esquema de tipo :javadoc:`DefaultEditableFeatureType <DefaultEditableFeatureType>` en el necesario::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("ID", "INTEGER", 10)
		schema.append("NAME", "STRING", 20)

		dbf_1 = createDBF(schema)
		
		dbf_schema = dbf_1.getSchema() # DefaultEditableFeatureType

		# Nuevo esquema basado en el de otra capa
		new_schema = createSchema(dbf_schema)
		new_schema.append("CODE", "STRING", 5)
		
		dbf_2 = createDBF(new_schema)


Una forma de acceder a los descriptores contenidos en el esquema es mediante ``schema.get("Campo")``. También podemos iterar sobre ellos. En el siguiente ejemplo, vemos la forma de añadir diferentes campos de diferentes tipos a un esquema nuevo. Hemos preparado una función que itera sobre ellos para mostrar una información completa sobre el esquema que le pasemos como parámetro, quedando::

	from gvsig import *
	from gvsig.geom import *

	def showInfoFeatureType(schema):
		#Show schema
		attrSchema = schema.getAttrNames()
		print "Schema attr: ", attrSchema
		#First field in schema
		field1 = schema.get(0)

		# Description of the fields
		print "\nFields description"
		for field in schema:
			print " Name: ", field.getName(),
			print " \tDataTypeName: ", field.getDataTypeName(),
			print " \tType: ", field.getType(),
			print " \tSubType: ", field.getSubtype(),
			print " \tPrecision: ", field.getPrecision(),
			print " \tSize: ", field.getSize()
			
			if field.getDataTypeName() == 'Geometry':
			  geomType = field.getGeomType()
			  print " \tGeom Name: ", geomType.getName()
			  print " \tGeom FullName: ", geomType.getFullName()
			  print " \tType: ", geomType.getType()
			  print " \tSubType: ", geomType.getSubType()
			  print " \tGeometryClass: ", geomType.getGeometryClass()
			  print " \tDimension: ", geomType.getDimension()

	def main(*args):

		schema = createFeatureType()

		schema.append("ID", "INTEGER", 10)
		schema.append("NAME", "STRING", 20)
		schema.append("AREA", "DOUBLE", 20, 10)
		schema.append("FECHA", "DATE", 20)
		schema.append("ACTIVE", "BOOLEAN")
		schema.append("GEOMETRY", "GEOMETRY")
		schema.get('GEOMETRY').setGeometryType(POINT, D2)

		shape = createShape(schema, prefixname="date")
		currentView().addLayer(shape)
		showInfoFeatureType(schema)
		
Por consola se muestra::

	Schema attr:  [u'ID', u'NAME', u'AREA', u'FECHA', u'ACTIVE', u'GEOMETRY']

	Fields description
	 Name:  ID  	DataTypeName:  Integer  	Type:  4  	SubType:  None  	Precision:  0  	Size:  10
	 Name:  NAME  	DataTypeName:  String  	Type:  8  	SubType:  None  	Precision:  0  	Size:  20
	 Name:  AREA  	DataTypeName:  Double  	Type:  7  	SubType:  None  	Precision:  4  	Size:  20
	 Name:  FECHA  	DataTypeName:  Date  	Type:  9  	SubType:  Date  	Precision:  0  	Size:  20
	 Name:  ACTIVE  	DataTypeName:  Boolean  	Type:  1  	SubType:  None  	Precision:  0  	Size:  0
	 Name:  GEOMETRY  	DataTypeName:  Geometry  	Type:  66  	SubType:  Geometry  	Precision:  0  	Size:  0
		Geom Name:  Point2D
		Geom FullName:  Point:2D
		Type:  1
		SubType:  0
		GeometryClass:  <type 'org.gvsig.fmap.geom.jts.primitive.point.Point2D'>
		Dimension:  2

Modificando esquemas
--------------------

En el siguiente ejemplo, vamos a modificar un esquema::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("ID", "INTEGER", 10)
		schema.append("NAME", "STRING", 20)
		schema.append("CODE", "STRING", 2)
		
		
		# By index
		schema.remove(0)
		print "Remove descriptor ID: ", schema.getAttrNames()

		# By descriptor
		rm = schema.getEditableAttributeDescriptor("CODE")
		schema.remove(rm)
		print "Remove descriptor CODE: ", schema.getAttrNames()

		# Add geometry field
		schema.append("GEOMETRY", "GEOMETRY")
		schema.get("GEOMETRY").setGeometryType(POINT, D2)
		print "Add geometry field: ", schema.getAttrNames()
		
Muestra por consola lo siguiente::

	Remove descriptor ID:  [u'NAME', u'CODE']
	Remove descriptor CODE:  [u'NAME']
	Add geometry field:  [u'NAME', u'GEOMETRY']
		
		
Esquema para capas vectoriales
------------------------------

Para la creación de una capa vectorial sería un ejemplo muy similar, solamente debería de contener un campo ``GEOMETRY`` de tipo ``GEOMETRY``. Después de crear este campo, tenemos que establecer el tipo de geometría que contendrá. 


En este ejemplo mostramos el caso típico de creación de una capa vectorial nueva::

	from gvsig import *
	from gvsig.geom import *

	def main(*args):

		schema = createFeatureType() # DefaultFeatureType

		schema.append("ID", "INTEGER", 10)
		schema.append("NAME", "STRING", 20)
		schema.append("GEOMETRY", "GEOMETRY")
		schema.get("GEOMETRY").setGeometryType(POINT, D2)

		shape = createShape(schema)
		currentView().addLayer(shape)
		

