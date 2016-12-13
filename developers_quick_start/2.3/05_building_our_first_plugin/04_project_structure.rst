
Estructura de un proyecto
-------------------------

..  tip::
    Puede resultarle útil consultar la documentación existente
    en la *Guía para desarrolladores* relacionada con esto, que 
    puede encontrar en la sección 
    `Estructura de un proyecto en gvSIG`_ asi como en
    `Cosas a tener en cuenta antes de desarrollar un plugin`_ .

.. _`Cosas a tener en cuenta antes de desarrollar un plugin` : http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/gvsig-devel-guide/2.0.0/crear-un-proyecto-para-gvsig/que-cosas-debemos-tener-en-cuenta-antes-de-desarrollar-nuestro-plugin

.. _`Estructura de un proyecto en gvSIG` : http://docs.gvsig.org/plone/projects/gvsig-desktop/docs/devel/gvsig-devel-guide/2.0.0/crear-un-proyecto-para-gvsig/estructura-de-un-proyecto-en-gvsig

Vamos a echarle un vistazo al código del proyecto. 
Los proyectos que encontraremos son:

- *org.gvsig.landregistryviewer*. Se trata de un proyecto Maven con varios subproyectos. En ellos
  encontraremos dividido, la parte de la lógica, con su API
  e implementación, así como la parte de interface de usuario asociada a los componentes
  de la lógica, también con su API e implementación, y un proyecto que nos permitirá
  probar esa lógica e interfaces de usuario sin necesidad de arrancar gvSIG. En general
  los distintos componentes que encontremos aquí serán independientes del framework
  de Andami así como de los plugins de gvSIG, siendo dependientes únicamente de otras
  librerías. Los proyectos que nos encontraremos serán:
  
  - *org.gvsig.landregistryviewer.lib* con la lógica de nuestro proyecto

    - *org.gvsig.landregistryviewer.lib.api*

    - *org.gvsig.landregistryviewer.lib.impl*

  - *org.gvsig.landregistryviewer.swing* con el interface de usuario de nuestros
    componentes lógicos.

    - *org.gvsig.landregistryviewer.swing.api*

    - *org.gvsig.landregistryviewer.swing.impl*

  - *org.gvsig.landregistryviewer.main*, nuestro *main* de pruebas.

  - *org.gvsig.landregistryviewer.app*. Este proyecto contendrá la implementación de los distintos plugins que 
    se van a añadir a gvSIG. Normalmente usará los componentes de los proyecto *org.gvsig.landregistryviewer.lib* y *org.gvsig.landregistryviewer.swing*, y se encargará de la integración de estos en gvSIG. En el ejemplo solo hay un subproyecto, *org.gvsig.landregistryviewer.app.mainplugin* ya que vamos a aportar un único plugin. Si tuviésemos necesidad de aportar más de un plugin tendríamos un subproyecto por plugin.

    Tendremos que tener especial atención a la hora de decidir cuantos plugins vamos a tener en nuestro proyecto. Normalmente nos será más comodo tener un solo plugin; sin embargo es aconsejable dividir nuestro proyecto en plugins que aporten funcionalidad independiente teniendo cuidado de no crear demasiados plugins muy pequeños. Deberemos mantener un equilibrio entre un solo plugin monolitico que aporte toda nuestra funcionalidad o tener muchos plugins muy pequeños. 
    
    Hay que tener en cuenta que el plugin es la unidad minima que puede instalar un usuario, y una division adecuada en distintos plugins puede permitir al usuario instalar mas o menos funcionalidad. Por ejemplo, dentro de proyecto *org.gvsig.chart*, podemos encontrar tres plugins distintos:
    
    - *org.gvsig.chart.app.mainplugin*, que aporta la funcionalidad básica de manejo de documentos de graficas.
    - *org.gvsig.chart.app.layoutplugin* aporta la integración de los documentos de graficas en el layout.
    - *org.gvsig.chart.app.legendplugin* aporta la integración de los documentos de graficas en las leyendas de las capas.
    
    Estas tres funcionalidades podian haber estado integradas en un solo plugin; pero se prefirio dividirlo en tres para facilitar el mantenimiento y dar al usuario la posibilidad de poder instalar, por ejemplo, la integracion con la leyenda sin tener que instalar la integracion con el layout.
    
