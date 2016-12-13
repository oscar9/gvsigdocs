

Nuestro primer proyecto
-----------------------

Para gvSIG 2.3.1 se dispone de un proyecto *base* que usar para la creacion de sus proyectos,
y que nos va a permitir disponer de un ejemplo para las principales formas de interaccionar
con gvSIG. Normalmente usaremos este proyecto como plantilla para crear nuestros proyectos.

Este proyecto podemos encontrarlo en el svn de gvsig, en la url::

  http://devel.gvsig.org/svn/gvsig-plugintemplates/org.gvsig.landregistryviewer/trunk/org.gvsig.landregistryviewer/

Sin embargo es aconsejable comprobar si en::

  http://devel.gvsig.org/svn/gvsig-plugintemplates/org.gvsig.landregistryviewer/tags/

Existe una version especifica para la version de gvSIG que vamos a usar. En nuestro caso, para gvSIG 2.3.1 si que existe y usaremos esta::

  http://devel.gvsig.org/svn/gvsig-plugintemplates/org.gvsig.landregistryviewer/tags/gvsig-desktop-2.3.1-2501/org.gvsig.landregistryviewer/
  

Antes de empezar, deberemos realizar unas pocas tareas de configuracion:

- Disponer de un JDK 1.8 instalado y accesible en el PATH.

- Disponer de cliente de svn accesible en el PATH. 

- Disponer de maven accesible en el PATH.

- Configurar donde se encuetra la instalacion de gvSIG sobre la que vamos a trabajar.


Suponiendo que ya estan instalados y configurados el JDK, el cliente de svn, y maven, vamos
a ver como configurar donde se encuentra la instalacion de gvSIG que vamos a usar.

En el HOME de nuestro usuario deberemos tener un fichero **.gvsig-devel.properties**. Se trata de un
fichero properties standard de java. En el debera haber una entrada de clave **gvsig.product.folder.path**
que indicara el path donde esta la instalacion de gvSIG.

Deberemos tener especial cuidado de que tengamos permisos de escritura en la carpeta de la instalacion
de gvSIG que vayamos a utilizar ya que de lo contrario se produciran error en el despliegue de nuestro
plugin.

Procederemos a ejecutar::

  ~ $ cd $HOME/devel
  ~ $ svn checkout http://devel.gvsig.org/svn/gvsig-plugintemplates/org.gvsig.landregistryviewer/tags/gvsig-desktop-2.3.1-2501/org.gvsig.landregistryviewer/
  ...salida del comando svn...
  ~ $ cd org.gvsig.landregistryviewer
  ~ $ mvn install
  ... salida del comando install...



..  note::
    Si estamos utilizando MS Windows deberemos tener especial cuidado con algunos detalles:
    
    - Podemos tener problemas con el tamaño de las rutas, en gvSIG se utilizan rutas muy largas.
      Es recomendable que cree una carpeta en el raiz de la unidad, por ejemplo **C:/devel** y
      usar esta para trabajar sobre ella.
    - Normalmente la instalacion de gvSIG se realizara en modo administrador, y nuestro usuario no dispondra
      de permisos para sobreescribir archivos y carpetas de esta, ademas de que puede contener rutas con 
      *espacios*  que puede ocasionarnos problemas de despliegue de nuestros plugins. 
      Para evitar problemas la sugerencia es que creemos la carpeta  *C:/devel/gvSIG-desktop* y copiemos 
      en ella la instalacion de gvSIG.
    - A la hora de crear el fichero *.gvsig-devel,properties* podemos tener algun problema. Deberemos 
      crearlo en el HOME de nuestro usuario, normalmente *C:/Usuario/mi-usuario*. Y tener especial 
      precaucion con:
      
      - el fichero debe llamarse exactamente ".gvsig-devel.properties", sin extension ".txt", asegurese
          de que al crearlo no tiene extension ".txt".
      - Puede tener problemas para crear un fichero que comienze por ".". Una forma de hacerlo puede ser
          abrir el bloc de notas y guardar el fichero en la ruta y nombre indicado.
    

