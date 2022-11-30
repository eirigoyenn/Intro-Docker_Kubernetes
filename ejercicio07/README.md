# Respuestas

-Se estan ejecutando 2 contenedores.
-Uno esta basado en la imagen : nicopaez/jobvacancy-ruby:1.3.0 , y el otro en : postgres:14.4-alpine .
-En el docker compose se pueden leer las siguientes lineas : 
        * version es la version de docker compose.
        * servicios son los que voy a desplegar, en este caso una web y una DB.
            *Dentro de web tenemos la imagen base, variables de entorno,puerto donde se mapea y dependencia en la base de datos para que esa sea creada primero.Tambien vemos el link que se hace con la db.
            *Dentro de la DB tenemos la imagen base y una variable de entorno.
-Los contenedores se ven entre si por el comando link, que permite que ambos servicios se encuentren en la misma red. Este comando hace las definiciones necesarias para que ambos contenedores puedan accederse mutuamente.



