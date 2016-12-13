Creando y abriendo diferentes tipos de capas
============================================

openStore
---------

Capa de PostgreSQL::
    from gvsig.utils import openStore
    
    os = openStore('PostgreSQL',_entity_='Persistence%3APostgreSQLStoreParameters',
                                port='5432',
                                JDBCDriverClass='org.postgresql.Driver',
                                UseSSL='false',
                                Schema='public',
                                Catalog='',
                                URL='jdbc%3Apostgresql%3A%2F%2Flocalhost%3A5432%2Fej1',
                                BaseOrder='',
                                Workingarea=None,
                                CRS='EPSG:23030',
                                PKFields='gid',
                                BaseFilter='',
                                DefaultGeometryField='geom',
                                Fields='gid%2Cnombre%2Cpoblacion%2Cgeom',
                                Table='ciudades',
                                SQL='',
                                password='pg',
                                dbname='ej1',
                                host='localhost',
                                dbuser='postgres',
                                ProviderName='PostgreSQL')
                                
createLayer
-----------

Capa Shape::

    # encoding: utf-8

    import gvsig
    from gvsig import *
    from gvsig.utils import createLayer
    from gvsig import geom

    def main(*args):

        #Remove this lines and add here your code

        schema = createFeatureType()
        schema.append("GEOMETRY", "GEOMETRY")
        schema.get("GEOMETRY").setGeometryType(geom.POINT, geom.D2)
        
        layer = createLayer(schema=schema,
                            servertype="FilesystemExplorer",
                            layertype="Shape",
                            shpFile="/home/osc/temp/test1.shp",
                            CRS="EPSG:25830",
                            geometryType=geom.POINT
                            )
