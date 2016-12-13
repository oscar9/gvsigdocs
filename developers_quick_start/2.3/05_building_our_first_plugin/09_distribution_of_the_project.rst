
Distribuyendo nuestro proyecto
------------------------------

Una vez, tenemos nuestro desarrollo desplegado sobre un gvSIG podemos ejecutar ese
gvSIG y comprobar si funciona correctamente. Ahora bien, normalmente nuestro trabajo
no termina ahí. Lo normal es que tengamos que hacer llegar esos plugins a nuestros
usuarios, o incluso antes a testers que verifiquen que todo funciona correctamente.

Con la versión 2.0 de gvSIG se desarrolló un sistema de paquetes que nos permiten
distribuir plugins de forma que los usuarios puedan instalarlos fácilmente desde
el *administrador de complementos* de gvSIG.

Cada vez que compilamos nuestro proyecto usando *"mvn install"*, además de compilarlo
se realizan algunas funciones mas. Las que nos interesan ahora mismo son:

- Los plugins de gvSIG de este se despliegan e instalan en la 
  instalación de gvSIG con la que estamos trabajando de forma que con solo arrancar 
  gvSIG observamos que estos ya están funcionales.

- Además de desplegar e instalar nuestros plugins, se dejan los paquetes de estos
  en la carpeta "install" de la instalación de gvSIG.

El paquete de instalación generado podemos distribuirlo directamente a nuestros usuarios
o hacerlo llegar al proyecto gvSIG para que se exponga en el repositorio de complementos de gvSIG
y esté disponible a los usuarios directamente desde el *Administrador de complementos* a través
de la URL que muestra por defecto.

.. tip::

   Puede consultar el articulo `Crea tu propio gvSIG`_
   del blog de gvSIG para leer más sobre ello.

Así mismo, podríamos generar una distribución personalizada de gvSIG desktop que incluyese
una selección de los plugins *standard* junto con nuestros plugins de forma que podamos disponer 
de un instalable personalizado con nuestros plugins para entregar a nuestros clientes. 


.. _Crea tu propio gvSIG: http://blog.gvsig.org/2015/09/04/crea-tu-propio-gvsig-nuevo-plugin/


