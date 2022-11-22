## HEALTHCHECK:

El healthcheck se utiliza para verificar que los servicios dentro de un container esten corriendo. Esto se hace porque en caso de tener varios procesos corriendo, si uno se cae, el contenedor seguiria corriendo y nunca se daria cuenta.

## ONBUILD: 

Son instrucciones que se van a ejecutar cuando se este utilizando esa imagen como base para crear otra. Cuando se este creando esa nueva imagen, las instrucciones ONBUILD de la imagen base se van a ejecutar luego del FROM de la imagen base.

## VOLUME: 

Crea un volumen de data persistente en el contenedor.

