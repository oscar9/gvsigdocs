
La librería con la presentación
-------------------------------

Al igual que sucedía con la parte de la lógica, la presentación también estará dividida en 
dos proyectos, por un lado el API y por otro la implementación.

- org.gvsig.Landregistryviewer.swing.api

- org.gvsig.Landregistryviewer.swing.impl

De forma similar a como sucedía con la lógica en la presentación, en el proyecto del
API sólo tenemos los interfaces y clases abstractas que definen nuestro API. El API
de la parte de presentación está formado por el interface del *manager* junto con
una serie de clases abstractas que definen el API público de nuestros componentes,
generalmente componentes que extenderán al componente de swing JPanel. Son clases
abstractas y no interfaces debido a que swing no presenta un modelo de interfaces
para sus componentes. En nuestro ejemplo, el único componente que tendremos es el
componente visual asociado a una manzana, el *JLandregistryviewerBlockPanel*,que extiende de
*JPanel* añadiéndole a nivel de API un único método que nos permita obtener el
componente lógico *LandregistryviewerBlock* que tiene asociado en un momento dado.

En la parte de implementación nos encontraremos con la clase *DefaultJLandregistryviewerBlockPanel*
que recibe en su constructor la instancia de *LandregistryviewerBlock* de la que debe presentar
sus datos. En general la parte de presentación no tiene una complicación mas allá
de la propia que pueda tener el manejo de swing. Resaltar
que la parte de presentación no debería usar nada que no este expuesto en el API
de nuestra librería de lógica.

Cuando creemos paneles en gvSIG tenemos nuestra forma de aproximarnos al paradigma de *modelo-vista-controlador*.
Aconsejamos dividir nuestro paneles en dos clases. Por un lado la creación
del GUI (*vista*); creación de los componentes y colocacion de estos en el panel. Y por 
otro la parte de logica que maneja el interface de usuario, gestion de eventos y API
que ofrecemos de nuestro panel (*controlador*). La parte que se corresponda con el *modelo*
de nuestro GUI normalmente ya estara representada por un elemento del API de
nuestra libreria de logica. Asi en nuestro ejemplo tendremos:

- *DefaultJLandRegistryViewerBlockPanelLayout* que haria de *vista*
- *DefaultJLandRegistryViewerBlockPanel*, que haria las veces de *controlador*
- *LandRegistryViewerBlock*, del API de nuestra libreria de logica, que haria de *modelo*.

Para realizar el diseño de nuestros paneles en el proyecto gvSIG utilizamos la herramienta
*abeille*. Con ella contruimos nuestro GUI y generamos el codigo java de nuestra *vista*. Este
codigo java no debe ser modificado. Nuestro codigo lo introduciremos en el *controlador* que normalmente
haremos que extienda de la *vista*. Ademas
hay que tener especial precaucion de dejar junto a  *DefaultJLandRegistryViewerBlockPanelLayout.java*
el fichero *DefaultJLandRegistryViewerBlockPanelLayout.xml* generado con el *abeille*, y no 
solo guardarnos el java generado, para asi facilitar posteriores modificaciones del interface
de usuario.

Podemos encontrar la herramienta de *abeille* dentro de la propia instalacion de gvSIG, ya que
la herramienta de scripting hace uso de ella. El diseñador de formularios lo encontraremos en::

  gvSIG/extensiones/org.gvsig.scripting.app.mainplugin/abeille-2.1.0_M3

Y para arrancarlo lo haremos a traves de *designer.jar*.

Tambien podemos descargarnoslo desde la web de gvSIG:

  http://devel.gvsig.org/download/runtimes/abeille/formsdesigner-2.1.0_M3.zip
  




