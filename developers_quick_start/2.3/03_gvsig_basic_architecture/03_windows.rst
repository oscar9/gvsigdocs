

Ventanas
--------

..  tip::
    Aunque actualmente no existe documentación relacionada
    con esto para la versión 2 de gvSIG, en general, la
    documentación existente para gvSIG 1, será válida.
    Puede encontrarla en `Las ventanas de Andami`_

.. _`Las ventanas de Andami` : http://docs.gvsig.org/plone/docdev/docs/v1_0/gvsig/andami/las-ventanas-de-andami


*Andami*, además de proveer un mecanismo para la carga y gestión de *plugins*
dispone de un API para la gestión de las ventanas de la aplicación. Actualmente 
el paradigma usado para la gestión de ventanas es MDI. Sin embargo el propio
gestor de ventanas de gvSIG puede ser sustituido para proporcionar otras formas
de visualización (SDI, tabs,...).

Las principales entidades que vamos a encontrar en *andami* relacionadas con 
el manejo de ventanas serán:

- **MDIManagerFactory**, es la factoría donde se registra y de donde se obtiene
  la instancia del MDIManager (hace de Locator).
  
- **MDIManager**, representa al gestor de ventanas registrado en gvSIG. Tiene métodos
  para presentar o cerrar una ventana.

- **IWindow**, es el interfaz que debe implementar un JPanel para que se presente
  como una ventana en gvSIG.

- **WindowInfo**, lo suministran nuestros paneles a través del interfaz *IWindow*
  y define las características de nuestra ventana (título, tamaño, tipo,...).

En general siempre que no precisemos de un control fino sobre nuestras ventanas
usaremos el método *showWindow* del *MDIManager* para presentar una ventana
a partir de un *JPanel* estándar de swing.


