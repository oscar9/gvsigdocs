:versión:       1.2

En el raíz del proyecto de SVN deberá existir un fichero *README.txt*
que oriente a quien se descarga el proyecto sobre cómo debe compilarlo,
desplegarlo, empaquetarlo y actualizar las traducciones a otros idiomas.
Existe una plantilla_ en formato ReST_  para ser usada a tal efecto.

Para obtener la plantilla_ deberá utilizar el *código fuente* del documento.
Accederá a este desde el icono que aparece en la parte inferior de la
plantilla_ (`más información sobre cómo acceder a los fuentes de un
documento aquí`_).

Esta plantilla contendrá las siguientes secciones:

* *Introducción*

  Tendrá una lista de puntos con la enumeración
  de los distintos apartados que hayan en este fichero.


* *Información sobre versión*

  Deberá especificar:

  - Ficheros que se deben tocar cada vez que se requiera cambiar el número
    de versión del proyecto.
  - Que operaciones hay que realizar para mantener actualizado el número
    de versión.
  - En caso de que no siga el formato de número de versión o la política
    de actualización que usa gvSIG, deberá describir cual es esta.

  Es recomendable que lea el documento
  `Interpretación del número de versión en gvSIG`_ .

* *Instrucciones de compilación*

  Deberá cubrir:

  - Instrucciones para configurar el  workspace de eclipse.
  - Instrucciones de compilación del proyecto.
  - Instrucciones para que un desarrollador lo pueda desplegar
    sobre andami en caso de que se trate de un plugin de gvSIG.

  Recuerde especificar si debe descargarse y montar un workspace
  con gvSIG o si su extensión debe compilarse contra unos binarios
  de gvSIG.


* *Instrucciones de empaquetado*

  Aquí deberá indicar qué se debe hacer para empaquetar el
  proyecto, cubriendo tanto el empaquetado como extensión
  independiente, como dando instrucciones a realizar para
  empaquetarlo en una instalación de gvSIG.

  Las instrucciones de empaquetado deberán incluir, tanto
  el proceso de empaquetado en si mismo, como el mantenimiento
  de un *número de versión* único, y actualización de los ficheros
  que corresponda en el SVN para que quede reflejado donde corresponda
  el incremento del *número de versión*.

  Así mismo, deberá incluir las instrucciones necesarias para
  generar un *tag* en el sistema de control de versiones con
  los fuentes que se corresponden con ese *número de versión*,
  detallando la política de nombres a usar para el nombre del
  *tag*.

  Además deberá seguir una política bien definida sobre el nombre
  que da al binario con su distribución. Es imprescindible que en
  el nombre del binario quede especificado:

  - Su *proyecto*.
  - La *versión*.
  - La *plataforma*.

  Si sigue una política para el nombre de los binarios de su distribución
  distinta a la marcada por gvSIG deberá indicarla en esta sección.
  Puede consultar la política de gvSIG al respecto en
  `Nombrado de binarios para un plugin de gvSIG`_,

* *Notas sobre internacionalización*

  Que contendra los subapartados:

  * Dónde se encuentran las cadenas de traducción.

    Aquí se especificara donde se encuentran las cadenas de traducción
    que usa el proyecto así como en qué idiomas están disponibles.

  * Qué hay que hacer para incluir un nuevo idioma

    Deberá especificar aquí qué hay que hacer para añadir un
    nuevo idioma a las cadenas que ya existan en el proyecto.

.. _`Nombrado de binarios para un plugin de gvSIG` : /web/reference_catalog/lookupObject?uuid=ab2213e3b42ff25300be00b564b05c63
.. _`Interpretación del número de versión en gvSIG` : /web/reference_catalog/lookupObject?uuid=99467e3828f3fe1a25ef5b6c96d17a13
.. _plantilla : /web/reference_catalog/lookupObject?uuid=de4119b71d8952500c5a1f37ed60831b
.. _`más información sobre cómo acceder a los fuentes de un documento aquí` : /web/reference_catalog/lookupObject?uuid=529fc89cb1d4e7677ed4f3e2a16af644
.. _ReST : http://docutils.sourceforge.net/rst.html


.. list-table:: Registro de cambios
   :header-rows: 1

   * - versión
     - Descripcion

   * - 1.1
     - Modificadda la redaccion del parrafo sobre como obtener la plantilla

   * - 1.1
     - Se han extendido las intrucciones de empaquetado para que incluyan
       el mantenimiento del número de build y los *tags* correspondientes
       en el SVN.

   * - 1.2
     - Modificada la redaccion de la seccion *Instrucciones de compilación*
       para que quede mas claro que debe contener.

   * - 1.2
     - Cambios menores en la redaccion del parrafo que habla sore *tags*
       en el apartado *Instrucciones de empaquetado*

   * - 1.2
     - Reescritura del apartado *Informacion sobre versión*, añadiendo
       enlace a la documentacion que describe como interpretar el
       número de versión en gvSIG.

   * - 1.2
     - Añadida nota con la necesidad de una política de nombrado de
       binarios.


