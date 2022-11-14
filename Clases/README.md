 # NOTAS DE CLASE
 
 ##CLASE 1
 
 ##CLASE 2 
 -Con el -v path/local : path/docker lo que hago es mapear ambos directorios.
 -Cuando levanto un contenedor, este levanta una red por lo que tengo que hacer el mapeo de puertos.
 -Mayor beneficio de Docker -> Me permite empaquetar mi aplicacion dentro de una imagen. Puedo correr esa imagen luego en cualquier lugar. La aplicacion la empaqueto ahi.
 De esto se desprende que si uso las buenas practicas de Docker, de ahi puedo escalar y crecer mi aplicacion.
 -Un conetenedor esta aislado, me puedo comunicar con redes (-p) , volumes (-v) o con variables de ambiente.
	Puedo montar variables de ambiente dentro del contenedor. (-it es interactivo para poder meterme)
	Lo hago con :  DOCKER RUN -eDB_URL=xdxdxd -it nicopaez/pingapp:latest /bin/bash  (bin/bash es que va a ejecutar un bash para luego estar dentro del contenedor).
    con env estando dentro del interactivo las puedo leer.

 -Hay que tener consideraciones al crear una App que va a ser Dockerizada.
 -DIVE para inspeccionar imagenes.
 -Portainer para analizar un contenedor.
 -Extension para docker desktop : Lens, Logger, Disk Usage. Son muy utiles y estan en el MarketPlace.
 -Nota: el flag --rm al hacer el run de un contenedor, hace que se borre al detenerse.

 ### Como crear una imagen.
	una forma: Con docker build creo imagenes. Tengo que hacerlo con un archivo llamado Dockerfile.
	otra: utilizar el docker commit.(ejemplo en ppt)
	
	comandos usados : 	docker run -it node:8 /bin/sh  (Crea una imagen con node:8)
						git clone https://github.com/nicopaez/passwordapi.git /usr/src/app
						cd /usr/src/app
						npm install --only=production
						  -->aca ya cree la imagen que quiero guardar
						docker commit \
							-c 'WORKDIR /usr/src/app' \
							-c 'CMD ["npm", "start"] \
							XXXX passwordapi-node:2                 xxxx es el id del contenedor que estoy corriendo.
							
							--> luego del commit, ya cree la imagen !.
							
							
							
	
	docker commit -c 'WORKDIR /usr/src/app' -c 'CMD ["npm", "start"]' f7f798a9f214 passwordapi-node:2  // ejemplo q me funciono
					--- repetir el proceso porque no me funciono en la clase,

 
### Creando imagen con un Dockerfile(con el ejemplo del git clone de arriba )
	Siempre arranca con un FROM , a no ser que quiera no usar una imagen base.(y tendria q arrncar con un Alpine, ubuntu, etc)
	
	Luego defino el directorio de trabajo con el WORKDIR
	
	Con el COPY, copio algo de afuera del contenedor hacia adentro
	
	RUN es para ejecutar algo adentro del contenedor
	
	COPY nuevamente copia todo lo del directorio q estoy parado, ademtro del conetenedor ( con el . )

	EXPOSE es que el contenedor va a estar exponiendo algo en el puerto 3000
	
	CMD indica que se va  aejecutar cuando el contenedor levante. Lo que hace es levantar el node con npm start
	
	para buildear:
		docker buil -t "nombreimagen":"tag" .  (el punto indica q uso el dockerfile de ese directorio)
		
	-P hace un mapeo de puertos automatico
	
	en el ejemplo vimos q con un docker ps , se mapeo el puerto 55000 de la maquina con el 3000 del contenedor.
	

NOTA: Docker build tiene un cache -- por lo tanto esto sirve cuando algunas layers son iguales.

	Testeo poniendo un CMD con cualquier cosa:: no me tira error al buildear la imagen, porque el cmd se corre recien cuando 
												ejecutar la imagen. El CMD se toma como si fuera un string.
	

BUILD vs. COMMIT :  El mas usado es el BUILD. El mejor es el COMMIT.Es mas generico.
					Hay casos donde no se puede usar el BUILD, y ahi usas COMMIT.
					Un caso de no poder usar docker build es porque tenga que clickear luego de ejecutar algo para poder instalarlo.
					Si o si los instaladores deben admitir linea de comandos.

### LAYERS
	Las capas son modificaciones sobre la capa de la imagen.
	Todo lo q agregue, o modifique directorios de la imagen crean nuevas capas.
	EXPOSE y CMD solo generan metadata, no capas.

	Lo bueno de esto de las capaz, es que es eficiente ya q solo manda lo q es distinto. Las layeres q son iguales no se mandan o descargan
	si docker ya las tiene.
	Esto logra q por ejemplo la imagen node no se tiene varias veces, sino una sola.
	
	Lo clave es reutilizar lo mas posible las capaz, para que queden cacheadas.

### MULTI-STAGE
	Esto se implemento despues, para casos donde por ejemplo tengo que compilar algo.
	Ejmplo: en Java tenes JAVA como ejecutable y JAVAC para compilar.
	Pero en mi imagen seria al pedo tener JAVAC, ya que eso estaria productivo entonces no voy a compilar siempre ahi.
	
	Entonces lo q puedo hacer es solo tener el JRE(Runtime de java), pero le tengo que entregar 
	 mi java ya compilado.
	 
	Tengo dos FROM en el Dockerfile, en el primero lo uso para compilar usando un FROM con el sdk que tiene el compilador.
	 El segundo stage empaqueta la aplicacion. Asi no necesito descargarme JAVA ni DOTNET en mi maquina, ya que la compilacion se hace dentro de DOCKER.

### COMANDOS
	
	CMD-> tiene una opcion de entry point.
	ADD
	COPY
	USER-> para determinar con que usuario se va a ejecutar la aplicacion.
			Este usuario va a depender de quien ejecute la imagen.
			Con esto te evitas tener problemas de que el Dockerfile lo ejecute un usuario que no tiene permisos.

### Recomendaciones dockerfile
	
	Cuidado con las imagenes de base-> chequear q sea buena, y de autores verificados.
	Fijarse la cantidad de layers y su orden.
	Chequear desde que usuarios se ejecutan cosas. Con root me podrian generar problemas si quieren hackearme.
	Exponer puertos, para que quien vaya a correr la imagen sepa en q puerto se corre.
	
	
					
	
	
 
 
 
 
 
 
 
 
 
 
 
