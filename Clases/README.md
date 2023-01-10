 # NOTAS DE CLASE
 # Primer Parte
 
 ## CLASE 1
 
 ## CLASE 2 
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
	
	
## CLASE 3

Al dockerizar cualquier cosa, lo que logro es buildear siempre igual. Entonces solo necesito definir una variable de entorno para diferenciar los diferentes ambientes.
Siempre leer la documentacion, porque a a pesar que ande hay que hacer buenas practicas. Por ej una imagen de java la puedo usar, pero si no pongo java -jar esta mal conceptualmente.

Ejercicio:
	Levantar app y db. Al estar en contenedores separados, necesito juntarlos en la misma red para que se vean.
	
		'#!/bin/bash'

		docker network create jv1

		docker container run --name=db -e POSTGRES_PASSWORD=Passw0rd! --network=jv1 postgres:14.4-alpine

		docker container run --name=web -e PORT=3000 -e RACK_ENV=production -e DATABASE_URL='postgres://postgres:Passw0rd!@db:5432/postgres' -p3000:3000 --network=jv1 nicopaez/jobvacancy-ruby:1.3.0
				

### DOCKER COMPOSE
	
	Para meter todos los comandos en un unico archivo.

Obs: si ocupo espacio en el disco con volumenes de docker, necesito eliminarlos porque no se eliminan estos automaticamente si los tengo mapeados.
		extension disk usage para ver los volumenes y el espacio que usa docker.
	
	docker-compose es otro cli distinto a docker.
	docker esta en go, y docker-compose en python.
	Hoy en la nueva version de docker tengo docker compose adentro de docker. es como una v2.
		docker-compose es la vieja, docker compose ejecuta direcamente sobre docker ya q es la nueva. la vieja es un script de python q ejecuta cosas en docker.
	docker compose tiene algunas sintaxis nuevas.
	
En produccion no suelo usar docker-compose, porque no hay tanto beneficio.

Si quisiera puedo tener todo en un docker, hasta el runtime. Con solo tener la IDE me alcanza.

OBS: -d era demonizada , para que el container no te tome la maquina.

Ejemplo: 
	api rest en ruby, con el framework padrino y postgre como db.
	tiene dos DB.
	Crea una webapp con un dockerfile.
	la web app la fuerza a que se cree luego de las dos db.
	
	->docker-compose exec webapp /bin/bash  --con esto nos metemos a la aplicacion
	->bundle install  -- es como un npm install, para instalar todo.
	-> RACK_ENV=test bundle exec rake  -- para correr los tests.
	-> ahora para conectar la app ejectuto ./start_app.h
	->ahora tengo corriendo todo. ($PORT no anda en win, forzar el puerto q tiene la webapp en el docker compose)
	
Ejemplo testing:
	--buscar link.


El problema de Docker es que tengo un unico servidor. Ademas de que tengo que scriptear todo porque el servidor es remoto.
La escalabilidad tambien esta limitada, porque si quiero aceptar mas trafico estoy limitado por el servidor.
Con esto surgieron los orquestadores (elmas popular es Kubernetes). Lo que tengo es un conjunto de maquinas que tienen docker adentro. Yo le pido al orquestadores
	que tiene que hacer y el orq resuleve estos problemas de Docker.
	
	
# Segunda Parte

## CLASE 4

Orquestadores. Nosotros tabajamos con Kubernetes.
El orquestador me abstrae de la cantidad de nodos.

### Docker Swarm

La maquina nodo hace un swarm init, luego los nodos se unen haciendo un join con el id del manager.
En el manager creo mi app con docker stack deploy.El orquestador decide en que nodo va a poner la app.

### Kubernetes

Es todo por linea de comandos. Kubectl.
kubectl version --version server y client
kubectl cluster-info
kubectl run pingapp --image=nicopaez/pingapp:2.1.0 --> Correr un contenedor con esa imagen.
kubectl get all --> leer todo lo creado con k8s

no existen aplicaciones en kube, son PODS.

kubectl port-forward pingapp 34567:4567 --> mapeo de puertos.es bloquenate a la consola.

kubectl exec -it pingapp -- bash --> adentro del contenedor del pod.

### Concepts

Pods.

Namespace:
	kubectl create namespace balanz3
	kubectl get namespaces
	kubectl -n Balanz3 ... -> para hacer cosas sobre ese namespace
		(o cambiar a default con kubectl config set-context --current --namespace=balanz3)
	OBS: el comando config se configura con el archivo de /.kube/config .
	OBS: si quiero puedo configurar la variable KUBECONFIG con el nuevo archivo de configuracion.









	




	
	
	
	
 
 
 
 
 
 
 
 
 
 
 
