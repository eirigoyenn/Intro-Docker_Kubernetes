# Comandos utilizados

    docker build -t passwordapi-redhat:1.0 .

    docker run -p 8000:8080 -d passwordapi-redhat:1.0

#  Publicar la imagen en dockerhub

    docker tag passwordapi-redhat:1.0 enriqueirigoyen/passwordapi-redhat:1.0.0
	
	docker push enriqueirigoyen/passwordapi-redhat:1.0.0

[link a imagen en dockerhub](https://hub.docker.com/layers/enriqueirigoyen/passwordapi-redhat/1.0.0/images/sha256-f6ca7feb145281d8963d4014e0014f9f239eec51324674438bfee57c75f1f74f?context=repo)


