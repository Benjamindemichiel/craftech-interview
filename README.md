<img src="https://i.ibb.co/VM5MzBT/craftech-logo3.png=150x" width="250" height="250">

## Prueba 2 \ Local Deploy :rocket: 

###### Comenzando :octocat:

Estas instrucciones permitirán obtener una copia del proyecto en funcionamiento en su máquina local para propósitos de desarrollo y pruebas.

### Utilizando Docker Compose :whale2: 

Es posible utilizar docker-compose para realizar un despligue local de las aplicaciones. De esta manera es posible realizar pruebas sobre cambios en el codigo muy rapidamente y puede desplegarse en practicamente cualquier OS.

##### Pre-requisitos :clipboard:

```
Instalar Docker:
- https://docs.docker.com/engine/install

Instalar Docker-compose:
- https://docs.docker.com/compose/install
```
_Es necesario instalar los requisitos en su OS antes de seguir_

###### Descargar el repositorio actualizado 

```
git clone https://github.com/fede-r1c0/interview-craftech
```

###### Ir al directorio del contexto 

```
cd interview-craftech/deployment/local/
```
#### Desplegar docker-compose :rocket:

```
docker compose up -d 
```

# 

### Utilizando un cluster de :whale: Docker Swarm :whale: #

Tambien es posible desplegar un cluster local con Docker Swarm. Docker-machine permite virtualizar los nodos de un cluster en VirtualBox y estos son gestionados a traves de Docker. Swarm permite probar funciones como escalamiento y high aviability de forma local sin mucha complejidad. Este entorno puede desplegarse tanto en Linux, como en MacOS o Windows.

#### Pre-requisitos :clipboard:

###### En MacOS o Windows :computer:

```
Si Docker Desktop se encuentra instalado no es necesario instalar ningun pre-requisito.
Doc -> https://docs.docker.com/machine/get-started/
```

###### En Linux :penguin:

```
Instalar Docker Machine: https://docs.docker.com/machine/install-machine/

Instalar VirtualBox https://www.virtualbox.org/wiki/Downloads
```

#### Crear las VM para el cluster 
Instalados los pre-requisitos es posible comenzar con el despliegue de los nodos.
Para este ejemplo utilizo tres nodos; el manager y dos workers, con el comando "docker-machine create" seguido del nombre del nodo se genera la VM en Virtual box.

```
docker-machine create manager1 

docker-machine create worker1

docker-machine create worker2
```

###### Una vez creadas las VM se pueden listar y verificar si estan activas
```
docker-machine ls 
```
###### Ingreso por SSH al nodo "manager1"
```
docker-machine ssh manager1
```
###### Verificar IP del nodo ya que servira para iniciar Swarm
```
ifconfig 
```
### Iniciar el cluster de Swarm :whale:

###### Sin salir de la sesion SSH del nodo "manager1" ejecutar swarm init:

```
docker swarm init --advertise-addr <IP de "manager1">  
```

###### Al iniciar swarm devolvera un output con comandos y el token para adoptar los nodos, ejemplo:
```
docker swarm join --token <token> <IP de "manager1">:2377
```
###### Una vez copiado el output es posible adoptar los workers
```
logout
```
#### Unir workers al cluster 

###### Ingresar por SSH al "worker1" y pegar el output brindado por swarm init

```
docker-machine ssh worker1

docker swarm join --token <token> <IP de "manager1">:2377

logout
```

###### Repetir la accion con el nodo "worker2"

```
docker-machine ssh worker2

docker swarm join --token <token> <IP de "manager1">:2377

logout
```

##### Comprobando la conformacion del cluster 

###### Volver a ingresar por SSH al nodo "manager1"

```
docker-machine ssh manager1
```

###### Listar los nodos que integran el cluster
```
docker node ls 
```

### Desplegar el stack con Swarm :whale: #

###### Descargar el repositorio actualizado 

```
git clone https://github.com/fede-r1c0/interview-craftech
```
###### Ir al directorio del contexto 

```
cd interview-craftech/deployment/local/
```
 
#### Ejecutar stack deploy :rocket:

``` 
docker stack deploy -c docker-compose.yml interview
```

###### Verificar si los servicios del stack se desplegaron correctamente 

```
docker service ls 
```
###### Se listaran los servicios que genero el stack.

###### Para verificar en que nodo esta corriendo cada servicio puede ejecutar;

```
docker service ps "ID or name"
```

### Escalar servicios en Swarm :whale: 

###### Es posible escalar servicios de un stack de Swarm, un ejemplo:
```
docker service scale "ID or name"="numero de replicas"
```
###### Si un servicio se llamara "interview_backend" y necesitamos tres replicas del mismo;
```
docker service scale interview_backend=3
```
###### Mas informacion en la documentacion oficial:
-[https://docs.docker.com/engine/swarm/swarm-tutorial/scale-service](https://docs.docker.com/engine/swarm/swarm-tutorial/scale-service)