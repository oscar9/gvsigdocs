

=======================================
Dependencias del proyecto
=======================================

:Version:       1.2

Para especificar las dependencias de nuestro proyecto
tendremos que rellenar la plantilla_ en formato ReST_
que existe para tal proposito.

Si estamos usando *maven* como herramienta de construcción
podremos suministrar el informe de dependencias que genera
este al contruir el *site* de nuestro proyecto, no siendo
necesario entonces seguir el formato indicado en la
plantilla_.

Esta plantilla_ describe las dependencias que tiene
nuestro proyecto en tiempo de *compilación*, *ejecución*
y *ejecución de tests*, así como las dependencias transitivas
y de dónde obtener las librerías que no forman parte ya
de gvSIG.

Es conveniente aclarar que distinguiremos entre *librerías
que forman parte de gvSIG* de las que no. Entendemos
por *librerías que forman parte de gvSIG* aquellas que
han sido construidas a partir de fuentes que están dentro
de alguno de los proyectos de gvSIG. Las librerías de
otros proyectos, estén usadas o no ya en algún proyecto de
gvSIG, a efectos de esta plantilla_, no se considera que
formen parte de gvSIG.

Encontraremos las siguientes secciones en la plantilla:

* *Project Dependencies*

  A la hora de especificar las dependencias, estas deberán ser
  dependencias con otras librerías, tanto de
  librerías que ya forman parte de gvSIG como de nuevas librerías
  que precise nuestro proyecto.

  Nunca deberemos indicar como dependencias otros proyectos
  de nuestro workspace de eclipse. Si fuese ese el caso,
  deberemos indicar como dependencias las librerías que
  genere ese proyecto al compilarse.

  * *Compile*

    Aquí especificaremos las dependencias en tiempo de compilación
    de nuestro proyecto.

  * *Runtime*

    Aquí indicaremos las dependencias de nuestro proyecto
    en tiempo de ejecución.

  * *Test*

    Aquí indicaremos las dependencias de nuestro proyecto
    para la ejecución de los test del proyecto.

  En proyectos para gvSIG previos a la versión 2.0.0, y que
  no utilicen *mavem* como mecanismo de construcción, si no
  tenemos claro que librerías son necesarias en cada momento
  indicaremos las mismas en los tres apartados, aunque siempre
  que se conozcan, es aconsejable especificarlas correctamente.

  * *Dependency Tree*

    En esta sección especificaremos las relaciones de
    dependencia que existan entre las distintas librerías
    del proyecto. Se trata de una vista en forma de arbol
    que nos muestre las dependencias transitivas de cada una
    de las dependencias de nuestro proyecto.

    Para las dependencias de *librerías que ya formen parte
    de gvSIG* no es preciso especificar las dependencias transitivas
    de estas, siendo obligatorio para todas las demas librerías de
    nuestro proyecto.

  * *Licenses*

    En esta sección listaremos las distintas licencias de las
    librerías que usa nuestro proyecto y no forman parte
    de gvSIG.

    Tendremos una entrada por licencia y dentro de esta las
    distintas librerías que se distribuyen con esa licencia.

    No incluiremos aquí las *librerías que ya forman parte de gvSIG*.

    Si hay librerías de las que desconocemos su licencia,
    las colocaremos en una entrada *unknow*, aunque esto
    puede ocasionar que nuestro proyecto no sea aceptado como
    un proyecto oficial de gvSIG.

  * *Dependency Repository Locations*

    En esta sección indicaremos para las librerías que no
    forman parte de gvSIG dónde está el repositorio oficial
    del que descargar la versión de la librería que precisa
    nuestro proyecto.

    Si estamos usando *maven* como herramienta de construcción
    podremos indicar uno de los repositorios oficiales de maven
    desde el que descargar la librería.

.. _plantilla : templates/project-dependencies.html
.. _ReST: http://docutils.sourceforge.net/rst.html

.. list-table:: Registro de cambios
   :header-rows: 1

   * - version
     - Descripcion

   * - 1.1
     - Modificadda la redaccion del parrafo sobre como obtener la plantilla

   * - 1.2
     - Adaptación a la nueva infraestructura
