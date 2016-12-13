 
Binarios
========

A continuación se enumeran los jar que genera DAL. A la hora de indicar las dependencias de un proyecto que use DAL, hay que tener en cuenta que sólo habrá que incluir las dependencias marcados como necesarios para compilar. A la hora de hacer un build ejecutable, habrá que incluir también aquellas dependencias necesarios en tiempo de ejecución (normalmente correspondientes a las implementaciones) que son necesarios para que funcione gvSIG.

Los jar que se necesitan cargar en tiempo de ejecución disponen de una clase Library que permite cargarlos e inicializarlos durante el arranque de la aplicación (o en cualquier momento posterior), para más información sobre este mecanismo se puede consultar el patrón Locator_.

Librería
--------

.. list-table::
   :header-rows: 1
   :widths: 50, 50, 20, 10, 60

   * - File
     - Description
     - Dependency Phase
     - Consumer
     - XML

   * - org.gvsig.fmap.dal-[version]_.jar
     - DAL api. If you are reading this, odds are you need this one
     - Compile
     - All
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal
             [version]
         

   * - org.gvsig.fmap.dal-[version]_-spi.jar
     - DAL spi. If you want to implement a new provider, this one is for you
     - Compile
     - Data provider
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal
             [version]
             spi
         


   * - org.gvsig.fmap.dal-2.0-[version]_-impl.jar
     - DAL implementation. Nothing to see here, move along
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal
             [version]
             impl
         

Dependencias de la librería
+++++++++++++++++++++++++++

.. list-table::
   :header-rows: 1
   :widths: 50, 50, 20, 10, 60

   * - File
     - Description
     - Dependency Phase
     - Consumer
     - XML

   * - org.gvsig.fmap.geometry-[version]_.jar
     - gvSIG's geometry model
     - Compile
     - DAL
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.geometry
             [version]
         


   * - org.gvsig.metadata-[version]_.jar
     - gvSIG's, metadata library
     - Compile
     - DAL
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.metadata
             [version]
         

   * - org.gvsig.tools-[version]_.jar
     - gvSIG's base tools and patterns
     - Compile
     - DAL
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.tools
             [version]
         


Implementaciones
----------------

Los siguientes jares son implementaciones que vienen de serie para gvSIG-Desktop.

Almacenes de tipo fichero
+++++++++++++++++++++++++

.. list-table::
   :header-rows: 1
   :widths: 50, 50, 20, 10, 60

   * - File
     - Description
     - Dependency Phase
     - Consumer
     - XML

   * - org.gvsig.fmap.dal.file-[version]_.jar
     - File stores common API
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
         

   * - org.gvsig.fmap.dal.file-[version]_-store.shp.jar
     - SHP provider *(extends of DBF provider)*
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dbf
         
         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.shp
         

   * - org.gvsig.fmap.dal.file-[version]_-store.dbf.jar
     - DBF provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dbf
         

   * - org.gvsig.fmap.dal.file-[version]_-store.dxf.jar
     - DXF provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dxf
         

   * - org.gvsig.fmap.dal.file-[version]_-store.dgn.jar
     - DGN provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dgn
         

   * - org.gvsig.fmap.dal.file-[version]_-store.dxf.legend.jar
     - DXF legend support
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dxf
         
         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dxf.legend
         

   * - org.gvsig.fmap.dal.file-[version]_-store.dgn.legend.jar
     - DGN legend support
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dgn
         
         
             org.gvsig
             org.gvsig.fmap.dal.file
             [version]
             store.dgn.legend
         




Almacenes de tipo base de datos
+++++++++++++++++++++++++++++++


.. list-table::
   :header-rows: 1
   :widths: 50, 50, 20, 10, 60

   * - File
     - Description
     - Dependency Phase
     - Consumer
     - XML


   * - org.gvsig.fmap.dal.db-[version]_.jar
     - Database stores common API
     - Runtime
     - GeoDB *(extGeoDB)*
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.db
             [version]
         

   * - org.gvsig.fmap.dal.db-[version]_.store.jdbc.jar
     - Generic JDBC provider
     - Runtime
     - GeoDB *(extGeoDB)*
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.db
             [version]
             store.jdbc
         

   * - org.gvsig.fmap.dal.db-[version]_.store.mysql.jar
     - MySQL JDBC provider *(extends JDBC provider)*
     - Runtime
     - GeoDB *(extGeoDB)*
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.db
             [version]
             store.jdbc
         
         
             org.gvsig
             org.gvsig.fmap.dal.db
             [version]
             store.mysql
         


   * - org.gvsig.fmap.dal.db-[version]_.store.postgresql.jar
     - PostgreSQL JDBC provider *(with PostGis support) (extends JDBC provider)*
     - Runtime
     - GeoDB *(extGeoDB)*
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.db
             [version]
             store.jdbc
         
         
             org.gvsig
             org.gvsig.fmap.dal.db
             [version]
             store.postgresql
         

Índices espaciales
------------------


.. list-table::
   :header-rows: 1
   :widths: 50, 50, 20, 10, 60

   * - File
     - Description
     - Dependency Phase
     - Consumer
     - XML


   * - org.gvsig.fmap.dal.index.spatial-[version]_-gt2.jar
     - Geotools2 quadtree provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.index.spatial
             [version]
             gt2
         

   * - org.gvsig.fmap.dal.index.spatial-[version]_-jsi.jar
     - JSI rtree provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.index.spatial
             [version]
             jsi
         

   * - org.gvsig.fmap.dal.index.spatial-[version]_-jts.jar
     - JTS quadtree provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.index.spatial
             [version]
             jsi
         

   * - org.gvsig.fmap.dal.index.spatial-[version]_-spatialindex.jar
     - SPTLIB rtree provider
     - Runtime
     - gvSIG
     - .. code-block:: xml

         
             org.gvsig
             org.gvsig.fmap.dal.index.spatial
             [version]
             spatialindex
         



.. _Locator: ../org.gvsig.tools/org-gvsig-tools-locator
.. [version] Versión de la librería, actualmente *2.0-SNAPSHOT*, pero la versión final será *2.0.0*
