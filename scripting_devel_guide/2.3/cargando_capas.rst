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

.. py:function:: loadDBF(filename):
    Open an existing dbf file

    :param str filename: absolute file path to the DBF file
    :return: dbf
    :type return: TableDocument

Recursos en rutas relativas: getResource()
------------------------------------------

En muchas ocasiones es necesario hacer referencia a ficheros que tengamos dentro de nuestro módulo, es decir, obtener la ruta completa de un fichero a partir de la ruta relativa en la que se encuentre nuestro script, independientemente de donde este instalado o del sistema. Para ello nos ayudamos de la función ``os.path.dirname(__file__)`` que devuelve la ruta de la carpeta de nuestro script en ejecución.

En este caso, ``__file__``, nos devuelve la ruta del script en ejecución::

    import gvsig

    def main(*args):
        print __file__

En mi caso la ruta es::

    /home/osc/gvsig-devel/master_scripts/testing_getResource.py

Con la función ``os.path.dirname()`` lo que hacemos es sacar el directorio donde se encuentra el script. Ejemplo de uso::

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

Hemos incorporado en la librería de ``gvsig`` una función ``gvsig.getResource()`` que nos ayuda a realizar esta operación de forma más sencilla.

Por ejemplo::

    # encoding: utf-8

    import gvsig

    def main(*args):

        x = gvsig.getResource(__file__,"testing.py")
        print "path: ", x
        print "type: ", type(x)

Podemos comparar la ruta por consola::

    path:  /home/osc/gvsig-devel/master_scripts/testing.py
    type:  <type 'unicode'>

Pudiendo añadirle tantos parámetros como carpetas sea necesario recorrer::

    # encoding: utf-8

    import gvsig

    def main(*args):

        x = gvsig.getResource(__file__,"Game","data","testing.py")
        print "path: ", x
        print "type: ", type(x)

Muestra por consola la ruta::

    path:  /home/osc/gvsig-devel/master_scripts/Game/data/testing.py
    type:  <type 'unicode'>

Podemos usar la función para otro tipo de rutas asegurándonos que será correcta independientemente del sistema::


    import gvsig

    def main(*args):

        x = gvsig.getResource("/home/osc/temp/","test1.dbf")
        print "path: ", x
        print "type: ", type(x)

Función de carga: loadLayer
---------------------------
Las funciones posteriores se apoyan todas en una función principal denominada ``loadLayer``.

Ejemplo de uso para un fichero raster::

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

Para trabajar con la creación y carga de ficheros DBF existen las funciones ``createDBF(schema)``  y ``loadDBF(dbfFile)``. Siendo un objecto :javadoc:`DefaultFeatureStore <DefaultFeatureStore>`

Ejemplo de uso::

    from gvsig import *

    def main(*args):

        schema = createSchema()
        schema.append("ID", "INTEGER")
        dbf = createDBF(schema, prefixname="summary")
        print dbf, type(dbf)
        path = dbf.getFullName()
        loadDBF(path)
