# Comandos utilizados

    docker build -t mypasswordapi:1.0 .

    docker run -p 8000:8080 -d mypasswordapi:1.0

#  Publicar la imagen en dockerhub

    docker tag mypasswordapi:1.0 enriqueirigoyen/passwordapi:1.0.0
	
	docker push enriqueirigoyen/passwordapi:1.0.0

[link a imagen en dockerhub](https://hub.docker.com/repository/docker/enriqueirigoyen/passwordapi)


