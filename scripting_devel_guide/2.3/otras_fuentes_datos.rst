Otras fuentes de datos
======================

En este apartado se incluirán diferentes funciones interesantes para trabajar con diferentes fuentes de datos. Como caso general vamos a introducir la función ``openStore(servertype, parameters)`` para la carga de fuentes de datos desde una ruta o enlace.

Si estos servicios ya se encuentran abiertos en nuestra Vista, podemos acceder a su contenedor de datos seleccionando la capa y accediendo a ella, por ejemplo, mediante un ``currentLayer()``.


PostgreSQL
----------

Desde Scripting podemos abrir conexiones a fuentes de datos de tipo ``PostgreSQL`` por ejemplo. La función a utilizar será ``openStore(servertype, parameters)``.


El ejemplo está realizado con una base de datos PostGIS creada en local en una máquina Ubuntu siguiendo los siguientes pasos (puede variar según versiones o distribuciones)::

    sudo apt-get install postgresql postgresql-contrib
    sudo apt-get install postgresql-9.5-postgis-2.2
    sudo apt-get install pgadmin3

    $ sudo su - postgres
    $ psql

    postgres=# select "version"();

    #Establecer contraseña entrando en psql
    postgres=# \password

    user: postgres
    pass: postgres

    # Create new db and table
    $ dropdb -U postgres ej1
    $ createdb -U postgres ej1
    $ psql -U postgres ej1
    psql (9.5.4)
    Digite «help» para obtener ayuda.

    ej1=# create extension postgis;
    CREATE EXTENSION
    ej1=# create extension postgis_topology;
    CREATE EXTENSION
    ej1=# select postgis_full_version();
    ej1=# select srtext from spatial_ref_sys where srid=23030;
    ej1=# create table ciudades (gid serial PRIMARY KEY, nombre varchar, poblacion integer);
    CREATE TABLE
    ej1=# alter table ciudades add column geom geometry (PointZ, 23030);
    ALTER TABLE
    ej1=# \d ciudades
                                            Tabla «public.ciudades»
      Columna  |          Tipo          |                          Modificadores
    -----------+------------------------+------------------------------------------------------------------
     gid       | integer                | not null valor por omisión nextval('ciudades_gid_seq'::regclass)
     nombre    | character varying      |
     poblacion | integer                |
     geom      | geometry(PointZ,23030) |
    Índices:
        "ciudades_pkey" PRIMARY KEY, btree (gid)

    ej1=# insert into ciudades (nombre, poblacion, geom) values ('Ciudad A', 10000, st_geomfromtext('POINTZ(700000 45000000 10)', 23030));
    INSERT 0 1
    ej1=# insert into ciudades (nombre, poblacion, geom) values ('Ciudad B',50000, st_geomfromtext('POINT(720000 4470000 15)',23030));
    INSERT 0 1
    ej1=# select * from ciudadeS;
     gid |  nombre  | poblacion |                                geom
    -----+----------+-----------+--------------------------------------------------------------------
       1 | Ciudad A |     10000 | 01010000A0F659000000000000C05C2541000000002A7585410000000000002440
       2 | Ciudad B |     50000 | 01010000A0F65900000000000000F92541000000003C0D51410000000000002E40

En el siguiente ejemplo vemos como introducimos todos los parámetros necesarios para una conexión PostgreSQL::

    from gvsig.utils import openStore

    def main(*args):
        os = openStore('PostgreSQL',port='5432',
                                    JDBCDriverClass='org.postgresql.Driver',
                                    UseSSL='false',
                                    Schema='public',
                                    Catalog='',
                                    URL='jdbc:postgresql://localhost/ej1',
                                    BaseOrder='',
                                    Workingarea=None,
                                    CRS='EPSG:23030',
                                    PKFields='gid',
                                    BaseFilter='',
                                    DefaultGeometryField='geom',
                                    Fields=None,
                                    Table='ciudades',
                                    SQL='',
                                    password='postgres',
                                    dbname='ej1',
                                    host='localhost',
                                    dbuser='postgres',
                                    ProviderName='PostgreSQL')

Una vez tenemos la conexión hecha, podemos comprobar que el tipo de objeto con el que estamos tratando es un :javadoc:`DefaultFeatureStore <DefaultFeatureStore>`, y por tanto, podemos trabajar de la misma forma que lo haríamos si fuera una capa vectorial, ya que el objeto es del mismo tipo::

    print "** os: ", type(os)
    features = os.features()
    for f in features:
        print f.getValues()
                                
Produciendo por consola una salida similar a::

    ** os:  <type 'org.gvsig.fmap.dal.feature.impl.DefaultFeatureStore'>
    {u'gid': 5, u'geom': POINT Z (720000.0 4600000.0 50.0), u'nombre': u'Ciudad A', u'poblacion': 30000}
    {u'gid': 6, u'geom': POINT Z (725000.0 4601000.0 10.0), u'nombre': u'Ciudad A', u'poblacion': 30000}
    {u'gid': 7, u'geom': POINT Z (725000.0 4651000.0 15.0), u'nombre': u'Ciudad A', u'poblacion': 30000}
    {u'gid': 8, u'geom': POINT Z (730000.0 4659000.0 20.0), u'nombre': u'Ciudad A', u'poblacion': 30000}
    {u'gid': 9, u'geom': POINT Z (722000.0 4620000.0 20.0), u'nombre': u'Ciudad A', u'poblacion': 30000}

Geojson
-------
Para cargar un fichero Geojson haciendo uso de la extensión de GDAL::

    # encoding: utf-8

    import gvsig
    from gvsig.utils import openStore

    def main(*args):

        os = openStore('OGRDataStoreProvider',file="/home/osc/gvsig-devel/example-data/countries.geojson",
                                             CRS="EPSG:4326",
                                             connectionString=None,
                                             layerName="OGRGeoJSON",
                                             defaultGeometryField=None,
                                             ignoreSpatialFilter=True,
                                             ProviderName="OGRDataStoreProvider"
                                             )
        print "geojson: ", type(os)
        features = os.features()
        for feature in features:
            print feature




Fichero LIDAR: Las
------------------

Cargando ficheros LAS con el driver de Whitebox::

    # encoding: utf-8

    import gvsig
    from gvsig.utils import openStore

    def main(*args):

        os = openStore('WhiteboxLASDataStoreProvider', file="/home/osc/LiDAR_6429129715959308627.las",
                                                  thinningResolution=None,
                                                  CRS="EPSG:25830",
                                                  thinningDivisor=None,
                                                  ProviderName="WhiteboxLASDataStoreProvider")

        print os
        features = os.features()
        print "Las size: ", features.getSize()


Raster
------

Cargando ficheros tif::

    # encoding: utf-8

    import gvsig
    from gvsig.utils import openStore

    def main(*args):

        os = openStore('Gdal Store',alphaband0None=None,
                                    visible=None,
                                    crs="EPSG:4326",
                                    uri="file:/home/osc/Descargas/wc2.0_5m_srad/wc2.0_5m_srad_12.tif",
                                    rmf_folder="/home/osc/Descargas/wc2.0_5m_srad",
                                    frame=None,
                                    selected_option=0
                                    )

        print "raster type: ", type(os)
