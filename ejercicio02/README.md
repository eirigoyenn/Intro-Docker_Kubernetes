 # Instrucciones utilizadas.
	
	docker pull nicopaez/pingapp:3.0.0
	
	docker login --username enriqueirigoyen --password *****
	
	docker tag nicopaez/pingapp:3.0.0 enriqueirigoyen/eirigoyen:1.0.0
	
	docker push enriqueirigoyen/eirigoyen:1.0.0
	
 # Sentencia para descargar imagen.
	
	docker pull enriqueirigoyen/eirigoyen:1.0.0