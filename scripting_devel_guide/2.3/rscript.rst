Lenguaje R
==========

Ejemplo de script en R (Renjin) mostrando información de una Vista::


    import (org.gvsig.app.project.documents.view.ViewDocument)
    import (org.gvsig.app.ApplicationLocator)

    main <- function() {

        cat( "Acceso a una Vista\n" );
        application <- ApplicationLocator$getManager();

        view <- application$getActiveDocument(ViewDocument);
        print(view$name);

    }