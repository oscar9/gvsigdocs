Introducción
============

..  note:: 
 
    Si va a desarrollar sobre otra versión de gvSIG compruebe si 
    existe documentación específica para esa versión.
    
.. figure:: images/developers_quick_start.png
   :align: center



El presente documento pretende ser una introducción al desarrollo con 
gvSIG 2.2.
No pretende profundizar en los entresijos de gvSIG, simplemente dar una
visión de las principales piezas de este y cómo pueden ser utilizadas para
construir nuestro primer desarrollo con gvSIG.

El lenguaje de desarrollo empleado en gvSIG es Java, con lo que si no 
está familiarizado con él es recomendable que antes lo haga. 
Como entorno integrado de desarrollo en el proyecto se emplea 
Eclipse_ y NetBeans_, así que también deberá estar familiarizado con alguno
de estos. Además se emplean otras herramientas como Maven_,
con las que es recomendable estar familiarizado, aunque no imprescindible
para una primera aproximación al desarrollo con gvSIG.

Además de estos conocimientos es **muy recomendable** que el desarrollador
esté familiarizado con la aplicación desde el punto de vista del usuario.
Puede encontrar en la web de gvsig.com_  la documentación de usuario, y
aunque no se corresponda exactamente con la versión de gvSIG con la que 
vaya a trabajar le ayudará a comprender mejor las distintas piezas de 
código.

Resumiendo, antes de continuar deberá conocer:

:Java: nivel medio o avanzado, necesario:
:Eclipse/NetBeans: necesario:
:Maven: recomendado:
:La aplicación gvSIG: recomendado:

Además de estos conocimientos deberá disponer:

- Un ubuntu (o derivado) 14.04, o un Windows 7.
- Eclipse_ (recomendable Helios o superior) o NetBeans_ (8 o superior).
- Maven_ version 3.0.5 (con versiones superiores hemos obserbado algun problema de compatibilidad)
- Conexión a internet para poder acceder a los repositorios públicos de maven.
- Un JDK 1.7 instalado y correctamente configurado, con el JDK 1.8, no compila correctamente gvSIG (la compilación con maven precisa de un JDK aparte del que incluya el IDE).
- Un gvSIG 2.2 final instalado.

..  tip::
    Puede encontrar informacion sobre las lista de desarrollo y en 
    general sobre todas las lista del proyecto en el documento `Listas de correo del proyecto`_ .

Si tiene problema para seguir este documento en algún momento puede preguntar en las lista de desarrollo del proyecto. Allí atenderemos las dudas que se le puedan plantear.

.. _`Listas de correo del proyecto` : http://www.gvsig.org/plone/community/mailing-lists/directory
.. _Ant: http://ant.apache.org
.. _Maven: http://maven.apache.org
.. _Eclipse: http://www.eclipse.org
.. _NetBeans: http://www.netbeans.org
.. _gvsig.com: http://www.gvsig.com

View source document

