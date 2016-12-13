.. _label-gvpy:

gvpy: Guía de uso
=================


**Lanzar geoprocesos desde el Módulo de Scripting**

Los geoprocesos de gvSIG y SEXTANTE ahora están accesibles con una sola línea de código y pueden formar parte de tus scripts. Podrás ejecutarlos desde la consola de Jython o desde el Scripting Composer, ubicados ambos dentro del módulo de Scripting. Este módulo de programación solo está disponible en la nueva versión de gvSIG 2.x en adelante.

.. note::

        Ya que existe la posibilidad de generar geoprocesos e incorporarlos a la caja de herramientas mediante Scripting, estos geoprocesos también serán accesibles desde gvpy.

Contenidos
----------

.. contents::

Librería gvpy
-------------

Para poder importar esta librería en tus scripts lo único que debes de hacer es escribir al inicio de tu código o en la Consola de Jython esto::

	import gvsig.libs.gvpy
	
o::

	from gvsig.libs import gvpy


Esto te permitirá utilizar la librería y acceder a todas sus funciones. 

.. note::
	
	En esta documentación damos por hecho que hemos usado el segundo tipo de importación al principio del script o de la consola.

Algoritmos
----------

Los algoritmos que están disponibles desde la librería de gvpy también los encontrarás en la Caja de Herramientas, puedes acceder a ella en :menuselection:`Herramientas --> Geoprocesamiento --> Caja de Herramientas`

Tendrás acceso tanto a los algoritmos de geoprocesamiento de gvSIG como de SEXTANTE.

En esta Caja de Herramientas podemos realizar búsquedas de algoritmos, pero también desde gvpy.

Si abrimos la Consola de Jython (Herramientas-Scripting-Jython Console) y escribimos `from gvsig.libs import gvpy` ya podemos acceder a sus funcionalidades. Estos comandos también podemos utilizarlos desde el Scripting Composer desde un script nuevo.

Por ejemplo, si queremos buscar un algoritmo que genere vectores aleatorios podemos hacer una búsqueda del tipo::

	gvpy.algSearch("aleatorias")

.. note::

	La descripción de los algoritmos, tanto en gvSIG-Geoprocesos y SEXTANTE, dependen del idioma en el que tengamos instalado gvSIG, así que para que funcionen las búsquedas se tendrán que realizar en este idioma determinado.

De los resultados que nos aparecen, podemos ver un algoritmo denominado: ``randomvector``

Parámetros del algoritmo
------------------------

Si queremos conocer más sobre el algoritmo podemos escribir el mismo comando que hemos usado antes::

    gvpy.algSearch("randomvector")

Si la búsqueda coincide exactamente con el nombre de un algoritmo, la información que nos muestra gvpy será la información sobre ese algoritmo, su descripción y sus parámetros::

	gvpy.algHelp("randomvector")
	* Algorithm help: 
	Capa vectorial con geometrias aleatorias
	*
	Usage: runalg( "randomvector",
								   COUNT[Numerical Value],
								   TYPE[Selection],
								   RESULT[output vector layer],
								   );


Lo mismo, podemos hacer la búsqueda en la **caja de herramientas**. Si entramos en el algoritmo podemos ver ayuda sobre lo que hace y abajo a la izquierda aparece el nombre clave del algoritmo (si no aparece, cierra la ventana y vuelve a abrir el geoproceso).

Línea de código
---------------

Ahora que **conocemos los parámetros necesarios** para ejecutar el algoritmo, los escribiremos en forma de línea de código.

Hay que **escribir gvpy** delante para acceder a la función que se encuentra dentro de la librería. El atributo RESULT, siempre que se refieran a los archivos de salida (output vector, etc), **no deberemos ponerlos**, ya que estos están gestionados por otro parámetro PATH que explicamos más adelante.

Ejemplo::
	
	gvpy.runalg("randomvector", COUNT= , TYPE= )

Estos parámetros del ejemplo se refieren a:
- **COUNT**: número de geometrías aleatorias que queremos generar
- **TYPE**: tipo de geometrías que queremos generar (polígono/línea/punto)

Ejemplo:: 
	
	gvpy.runalg("randomvector", COUNT=10, TYPE=0)

Este algoritmo **ya lo podríamos ejecutar** en nuestra consola de Jython o en nuestros scripts.

.. note::

	Recuerda que si no estableces el atributo ADDLAYER=False deberás de tener una **VISTA abierta** para la correcta ejecución del proceso, sino el programa no sabrá sobre qué espacio trabajar. Por lo general, antes de ejecutar tus scripts, abre un proyecto nuevo, abre una vista nueva, y entonces, accede al módulo de Scripting de la manera que desees.

Recomiendo escribirlo con el formato anterior pero también, siempre que guardemos el orden que nos dan los parámetros, podemos escribirlos sin especificar que atributo es.

Ejemplo::

	gvpy.runalg("randomvector", 10, 0)

Y para ser compatible con unas funcionalidades extra, también es posible si todos los parámetros los pasamos como texto con las comillas puestas:

Ejemplo::

	gvpy.runalg("randomvector", "10", "0")

Parámetros de entrada
---------------------

Tipos de capas / geometrías
+++++++++++++++++++++++++++

Por si nos olvidamos qué número corresponde a qué tipo de geometría o de capa, existen unas constantes dentro de gvpy para ayudarnos a recordarlas. 

En el ejemplo anterior, el parámetro `TYPE` que hace referencia a este tipo de polígonos, podemos escribirlo de diferentes maneras ayudándonos de estas constantes.

Tipo polígono: corresponde al valor 0, o también como gvpy.TYPE_POLYGON
Tipo línea: corresponde al valor 1, o también como gvpy.TYPE_LINE
Tipo punto: corresponde al valor 2, o también como gvpy.TYPE_POINT

Estos dos ejemplos son iguales::

	gvpy.runalg("randomvector",10, 0)
	gvpy.runalg("randomvector",10, gvpy.TYPE_POLYGON)

Parámetros de tipo Capa
+++++++++++++++++++++++

Si el **algoritmo nos pide un parámetro de tipo capa** (ya sea tabla, vectorial o raster), tendremos que cargar esta capa en una variable y pasarla como parámetro. 

En la **librería de gvsig** existen diversas formas. Podemos ver más información sobre su uso en el apartado de :ref:`Cargando capas <label-cargando_capas>`

Además, como explico en el siguiente apartado, **se pueden capturar las capas resultado de la ejecución de un algoritmo**, para poder ser usadas en otro apartado del script o ser pasadas como parámetro para otros geoprocesos.

Parámetros Multi input (en desarrollo)
++++++++++++++++++++++++++++++++++++++

Si son de tipo Vector se incluirán mediante una lista Ej. [vectorLayer1, vectorLayer2, ..... ]

Parámetros Raster + Banda
+++++++++++++++++++++++++

Si son de tipo raster + banda se incluyen con una tupla Ej. (raster, 1)

Ej. Usando multi input de rasters::

	runalg("vectorizetrees", [(raster1,1), (raster2,1)], "capa1", "1.0", "1.0", "#")

Parámetro Point
+++++++++++++++

Se puede pasar como texto: "100.0, 150.0" o como geometría de gvSIG `geom.createPoint(geom.D2, 100.0, 150.0)`.

Parámetros Fixed table
++++++++++++++++++++++

Se tienen que añadir como texto (string), con una sucesión de números a modo de lista.
Ej. ``LUT="1,1,1,0,0,0"``



Archivos de salida
------------------

Los archivos de salida (RESULT) son la capa o capas que generarán como resultado el ejecutar nuestro algoritmo, también salidas de tipo texto. Si no se especifica se guardarán en una carpeta temporal:
`C:\Users\Oscar\AppData\Local\Temp\tmp-andami`

Nosotros estableceremos la ruta que queremos con el comando PATH para indicar ruta y nombre. Explicación sobre PATH en el apartado de Atributos Extra.

Además, **podemos capturar estas capas** para poder seguir utilizándolas en nuestro script recogiendo la salida del algoritmo::

	capa = gvpy.runalg("randomvector",10,0)

De esta forma la variable `capa` contendrá las capas de gvSIG que son el resultado de lanzar el algoritmo. Si realizamos un `print capa` podremos ver como contiene una capa resultado.

Si el algoritmo generase dos o más capas como resultado, nos devolvería una lista con todas ellas, y podríamos acceder a ellas de la forma::

	print capa[0], capa[1], capa[2]

Atributos extra
---------------

A parte de los parámetros del algoritmo, podremos designar otros **atributos opcionales** que permitirán modificar otras variables tales como la ruta de salida, región de análisis, etc.

EXTENT
++++++

Define la región de análisis del algoritmo a utilizar. Por ejemplo en nuestros algoritmo anterior hará que las geometrías aleatorias estén todas contenidas en esta región.

Se escribirá: `EXTENT =` dentro del algoritmo

Podemos declararla de diversas formas:

- **Nombre de la Vista** (string): Buscará la Vista con ese nombre en nuestro proyecto. El comando en el que está basado es en: ``currentProject().getView(name)`. Ejemplo `EXTENT="Vista1"``

- **Vista** ( :javadoc:`DefaultViewDocument <DefaultViewDocument>` ): Le pasamos directamente la Vista y cogerá la región en la que las capas de esta se expanden. Ejemplo: ``EXTENT=currentView()``

- **Layer** ( :javadoc:`FLyrDefault <FLyrDefault>` ): Extensión de la capa ya sea vectorial o raster. Ejemplo: ``EXTENT=capa``, basado en nuestro ejemplo anterior cogería la extensión de la capa generada por nuestro algoritmo.

- **Lista** (Python list): Pasando las coordenadas de la región. Las esquinas inferior izquierda y superior derecha, incluyendo la coordenada Z. Ejemplo: ``EXTENT=[100,100,0,500,500,0]``

- **Por defualt**: Si no se le indica, intentará coger el ``currentView()``, Vista del proyecto actual. En caso de que este no contenga ninguna capa, cogerá las coordenadas predeterminadas de: ``EXTENT=[0,0,0,100,100,0]``

CELLSIZE y CELLSIZEZ
++++++++++++++++++++

Estos dos comandos se aplican también a la región de análisis, y son utilizados solamente para aquellos algoritmos que generen una capa de tipo Raster. Corresponderán al tamaño de las celdas en (x,y) y en (z).

.. note::

	Muchas veces en este tipo de algoritmo **salta un error porque la región de análisis es excesivamente grande**. Esto lo podemos **solucionar aumentando el tamaño de las celdas**

Ejemplo::

	CELLSIZE=10, CELLSIZEZ=1

PATH
++++

Con este atributo podemos designar el nombre y la ruta de las capas resultado.

Según las capas resultado que genere el algoritmo, esto lo podemos ver cuando utilizamos el comando gvpy.algHelp("") viendo cuantas capas de salida (output) tiene, nos lo devolverá de dos formas diferentes:
- Si solo devuelve una capa, podemos introducir la ruta como: `PATH="C://capa_01.shp"`
- Si devuelves dos o más, las podemos introducir en forma de lista: `PATH=["C://1.shp", "C://2.shp"]`

.. note::
        
        Si el fichero ya existe se podrucirá un error durante la ejecución del geoproceso.

OUTVIEW
+++++++

Nos permite seleccionar en que Vista queremos que se carguen los archivos de salida de nuestros algoritmos ejecutados.

Podemos introducirla tanto como por su nombre::

	OUTVIEW="Vista1"

o por el objeto de tipo Vista::

	OUTVIEW=currentView()

NAME
++++

.. warning::

	Anteriormente este atributo era denominado TOCNAME.
	
Este atributo nos permite modificar el nombre con el que aparecen las capas en el TOC. Es independiente del nombre que tenga la capa. Por ejemplo, nos permitiría acceder a la capa a través del nombre mediante `gvsig.currentView().getLayer(name)`.

Ejemplo::

	NAME="Resultado Vector 01"
	

ADDLAYER
++++++++
Si queremos generar una capa en el geoproceso pero no añadirla a ninguna vista, podemos establecer el parámetro ADDLAYER como ``True`` o ``False``. Por defecto será ``True``. 

.. note::
	
	Por lo general, es muy cómodo usar este atributo en False. Si queremos después añadir la capa a una vista especifica solo necesitaremos capturar la capa de salida del geoproceso y añadirla con ``view.addLayer(capa)``
	
Ejemplo::

	ADDLAYER=False
	
Ejemplo de uso::

    layer_result = g.runalg("vectorize", layer1, ADDLAYER=False)
	currentView().addLayer(layer_result)
	
OUTPUT_FILTER
+++++++++++++
En algunos geoprocesos la salida de capas es múltiple y puede realizarse en diferente orden la salida de estos resultados. Estos geoprocesos al ejecutarse desde la Toolbox añaden los resultados a la Vista que tenemos abierta, la diferencia entre sus capas viene dada en su nombre.

Para ejecutarlo desde Scripting una opción posible es establecer un filtrado del nombre de la capa resultado que queremos.

Por ejemplo, en el geoproceso de Kriging una de estas capas de salida establece en su nombre "interpolado", aquí vemos como hacemos para capturar solo esta capa de salida:
	
Ejemplo de uso::

	    proc1 = g.runalg("kriging", layer1, "0", "5.0", "3", "5", "0", "0.0", "100.0", "2.0", ADDLAYER=False, OUTPUT_FILTER="interpolado", EXTENT=layer1, CELLSIZE=0.5, CELLSIZEZ=0.5)

	

model2script: SEXTANTE model to Script
--------------------------------------

Convierte tus ficheros `sextante.model`, creados por el Model Builder, en scripts. Tan solo utiliza la función `gvpy.model2script` con la ruta del fichero model, y la ruta del nuevo fichero que vas a crear. Luego deberás de mover tu script a la carpeta de Scripting que está en:
``C:\Users\[user]\gvSIG\plugins\org.gvsig.scripting.app.extension\scripts``
o copiar y pegar en un nuevo script.

.. warning::
	
	Esta función esta en desarrollo y aún puede contener errores o resultados incompletos.

Ejemplo::

	gvpy.model2script("C://gsoc//test02.model", "C://gsoc//nuevoScript.py")
