.. _label-cargando_capas:

Cargando capas
===============

.. py:function:: loadShapeFile(shpFile, **parameters):
    
    Add an existing shape file to the view. Returns Layer shape file
	
    :param shpFile: absolute file path to the shape
    :type: shpFile: string
    :param CRS: projection code
    :type CRS: string
    :param gvViewName: name of the view where to be loaded
    :type gvViewName: string
    :param gvLayerName: name of the layer inside gvsig
    :type gvLayerName: string
    :return: shape
    :type return: Layer
 
.. py:function:: loadRasterFile(filename, **parameters):

	Add an existing raster file to the view. Returns Layer Raster file.
	
    :param str filename: absolute file path to the raster
    :param CRS: projection code
    :type CRS: string
    :param gvViewName: name of the view where to be loaded
    :type gvViewName: string
    :param gvLayerName: name of the layer inside gvsig
    :type gvLayerName: string
    :return: raster layer
    :type return: Layer

Recursos en rutas relativas
---------------------------

Un recurso para acceder a datos que tengamos en la carpeta de nuestro módulo, independientemente de donde lo tengamos instalado o del pc, es hacer uso de una función de Python para sacar la ruta absoluta de recursos necesarios para su ejecución. Esta ruta se calcula a partir de la ruta en la que se ejecuta el script.

Ejemplo de uso::

	# encoding: utf-8
	import os
	import gvsig

	def main(*args):
		"""Obtener ruta absoluta de ficheros en la carpeta de ejecucion del script"""
		
		path_script = os.path.dirname(__file__)
		print "Path script: ", path_script
		print "Ruta relativa: data/fichero.xx"
		print "Ruta absoluta: ", os.path.join(path_script, "data", "fichero.xx")

Salida por consola será::

	Path script:  c:\gvdevel\gvsig\gvSIG-desktop-2.3.0-2441-RC2-win-x86_64\home\gvSIG\plugins\org.gvsig.scripting.app.mainplugin\scripts
	Ruta relativa: data/fichero.xx
	Ruta absoluta:  c:\gvdevel\gvsig\gvSIG-desktop-2.3.0-2441-RC2-win-x86_64\home\gvSIG\plugins\org.gvsig.scripting.app.mainplugin\scripts\data\fichero.xx

De esta forma estaríamos accediendo a la carpeta ``/datos/`` dentro de nuestro módulo.

loadLayer
---------
Las funciones posteriores se apoyan todas en una función principal denominada ``loadLayer``.

Ejemplo de uso::

    layer = loadLayer('Gdal Store', crs=getCRS("EPSG:3042"),uri=File(r"C:\temp\mdt20_3_etrs89.tif").toURI(),gvViewName=currentView())
	
Capas Vectoriales
-----------------

Para cargar capas vectoriales usaremos la función :py:func:`loadShapeFile`. Esta es una función de apoyo que llama a la función ``createLayer()`` más genérica.

Ejemplo::

	from gvsig import *
	from java.io import File

	def main(*args):

		# Get resource path
		shape_path = os.path.join(os.path.dirname(__file__),"data","jaen.shp")

		# loadShapeFile function
		s1 = loadShapeFile(shape_path)
	   
		# LoadLayer function
		s2 = loadLayer("Shape", shpFile=xfile, CRS="EPSG:25830")

	
Capas Raster
------------

Para cargar capas raster usaremos la función :py:func:`loadRasterFile`. Esta es una función de apoyo que llama a la función ``createLayer()`` más genérica.

Si necesitamos transformar una ruta a formato ``uri`` podemos hacerlo apoyándonos en la clase de Java: ``java.io.File``:

Ejemplo::

	from gvsig import *
	from java.io import File
	
	def main(*args):

		# Load Raster File
		raster_path = os.path.join(os.path.dirname(__file__),"data","mdt_jaen.tif")
		r1 = loadRasterFile(raster_path)
		
		raster_uri = File(os.path.join(os.path.dirname(__file__),"data","fichero.xx")).toURI()
		r2 = loadLayer("Gdal Store", uri=raster_uri)
		
Ficheros DBF
------------

Para trabajar con la creación y carga de ficheros DBF existen las funciones ``createDBF``  y ``loadDBF``. Siendo un objecto :javadoc:`DefaultFeatureStore <DefaultFeatureStore>`

Ejemplo de uso::

	from gvsig import *

	def main(*args):

		schema = createSchema()
		schema.append("ID", "INTEGER")
		dbf = createDBF(schema, prefixname="summary")
		print dbf, type(dbf)
		path = dbf.getFullName()
		loadDBF(path)