 
Introducción
============

Este documento describe la capa de acceso a datos de gvSIG (denominada Data Access Library, DAL para abreviar) tras la reingeniería realizada en la rama v2_0_0_prep. 

Con DAL se pretende dotar a gvSIG de una capa de abstracción que permita al núcleo de la aplicación operar de forma homogénea con diferentes fuentes y formatos de datos. Su arquitectura se basa en la flexibilidad y la robustez, y por ello aspectos como el desacoplamiento y la trazabilidad son esenciales en su diseño. Algunas características de DAL:

- Es extensible a través de un marco común.
- Reduce el acoplamiento entre las distintas capas de la aplicación y los proveedores de datos. 
- Mejora la trazabilidad en el acceso a datos.

