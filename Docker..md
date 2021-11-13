docker --version

docker version

docker info // All Info About Our Docker Host

docker image build -t rahuljaiswal1000/gsd:first-container .

( -t on the Image build Command is the tag name : dockerId/repositoryname:Version

If We do not specify version , it will be latest

. at the end tells docker that When We fire this Commandand All the Files it Need specially the Dockerfile are in the Current directory from Where I am Running this Command
)

docker image ls

(Image Listing )

docker image push rahuljaiswal1000/gsd:first-container

(Pushing to DockerHub )

docker image rm rahuljaiswal1000/gsd:first-container

(Deleting a local Image)

docker image tag rahuljaiswal1000/gsd:first-container rahuljaiswal1000/gsd:latest

(Image tagging as Latest)

docker image inspect rahuljaiswal1000/gsd:first-container

(Getting All the Layers in an Image )

docker container run -d --name web -p 8000:8080 rahuljaiswal1000/gsd:first-container

(

1. -d : Run This Container in the background detached from my terminal

2. Port mapping : map port 8000 on the Host to 800 port the app is listening in the Container

3. Any traffic hitting Host on 8000 will be sent to 8080 port in the Container app

4. By Default if We don’t use Any Url of a different registry before Image Name (rahuljaiswal1000/gsd:first-container) , Docker will think of Docker Hub

5. As in the Image , First docker tries to Find the Image Locally , did not find so went to DockerHub , Pull it and create Container from it
   )

docker container ls

(Listing Currently Running container)

docker container stop web

( Stopping a Container )

docker container ls -a

(Listing All containers whether stopped or Running Now)

docker container start web

(ReRunning a stopped Container )

docker container rm web

( Deleting a container )

docker container run -it --name test rahuljaiswal1000/gsd:first-container

(Running Containers in shell integrated mode )

docker exec -it containerId /bin/sh : Go inside a Running Container

(Do docker Container ls, If there is /bin/sh or sh command use that , otherwise /bin/sh )

## A Windows Container : microsoft/iis Running on port 0080

## Docker Compose Commands

docker compose up -d : Build Images && Start All Containers

docker compose down : remove all containers

docker compose build : Build/Rebuild Images

docker compose logs : Logs of Containers Running

docker compose start : Start All Containers if stopped

docker compose stop

## Volume

docker container run -d --name web88 --mount source=ubervol,target=/vol -p 8000:8080 rahuljaiswal1000/gsd:first-container // Run a new container and create and attch a new Volume to it

docker container ls // Check the Container is Running Now

docker exec -it web88 /bin/sh // get inside the Container Now

Add new data in a New File

/usr/src/app # ls -l /vol/

total 0

/usr/src/app # echo "some data" > /vol/newfile

/usr/src/app # ls -l /vol/

total 4

-rw-r--r-- 1 root root 10 Oct 28 03:20 newfile

/usr/src/app # cat /vol/newfile

some data

Press Cntrl+P+Q to get outside the Container

docker Container stop web88

docker container run -d --name web98 --mount source=ubervol,target=/vol -p 8000:8080 rahuljaiswal1000/gsd:first-container // Run a New Container with the same Volume

Attached

docker exec -it ddb332b9757c /bin/sh // get inside the new Container and Check the Data Now

/usr/src/app #

/usr/src/app # cat /vol/newfile

some data

/usr/src/app #

docker volume ls : Listing All Volumes

docker volume inspect volumeId

docker container run -d --name web88 /D/ABCDE/:/newVolumeDirectory -p 8000:8080 rahuljaiswal1000/gsd:first-container

( Mounting Host Directory to /newVolumeDirectory Directory inside WorkingDiirectory in Container )

docker container run -d --name web88 -v /newVolume -p 8000:8080 rahuljaiswal1000/gsd:first-container

( Create a new Volume and attach it to the Container at /newVolume Directory )

docker container run -d --name web98 --volume-from rahuljaiswal1000/gsd:first-container -p 8000:8080 rahuljaiswal1000/gsd:first-container

(Share the previous Container Volume to this New Container )

## Docker Swarm

docker swarm init : For Docker Desktop. Docker Desktop is a Single Node Swarm

Otherwise,

docker machine create --driver virtualBox manager1

docker machine ssh manager1

docker swarm init --advertise-addr <IP_Address of Manager>

( For Multi-Node Swarm )

docker swarm-join join-token .........

(Joining a swarm as worker or Manager )

docker nodes ls : This Command must be executed at Manager Node to get swarm info

docker info : This Command must be executed at Manager Node to get swarm info

(Listing All Nodes )

docker node inspect nodeId

docker nodes inspect self --pretty: Inspecting itself

docker nodes promote nodeId : this Node will be promoted to Reachable State

docker node update --availability pause nodeId : Updating Node Availability

## Docker Services

docker service create --name service1 --replicas 5 imageName : This command must be executed at Manager Node

docker service inspect serviceId

docker service scale servicename =5 : scaling the Service to 5 Replicas

docker service rm serviceId : removing a Service

http://Node/Machine IP Address : Port : Accesssing a Service on a Port

http://localHost:Port : Accesssing a Service on a Port

## Docker Secrets

docker secrets create secret_Name=value : Crete secret through CMD

docker secrets create secret_Name fileNameContainingSecret : Crete secret through CMD

docker service update --secrets-add secret_Name serviceId : Updating a service To use a secret

## Docker Stacks

docker stack deploye -c docker-compose.yml stackName : docker-compose.yml File must be present in the Current directory

docker stack ls

docker stack services stackName : Listing All Services in the Stack

docker stack ps stackName : Listing All Services with All Tasks/Replicas in the Stack

## Docker Logs

docker logs containerId

docker logs containerId -f :show Following Logs

docker logs containerId --tail N : last N Logs

docker logs containerId | grep -i error : Show only error Logs

docker compose logs

docker info : To get the current logging Driver on Docker Host

docker container inspect ContainerId : To get Logpath of a container

## Docker Storage Driver

docker info : To get the Current Storage Driver

## Docker Networking

docker network create --driver bridge netWorkName

docker network inspect netWorkId : to get all the Containers inside this Network

docker container inspect ContainerId : To get Network of a Container

docker container run -d --name web -p 8000:8080 --network netWorkName rahuljaiswal1000/gsd:first-container : Joining an existing Network

docker container run -d --name web -p 8000:8080 --network host rahuljaiswal1000/gsd:first-container : Joining host Network

docker container run -d --name web -p 8000:8080 --network none rahuljaiswal1000/gsd:first-container : disable Network in the Container

docker service create --name service1 --network anOverNetwork --replicas 5 imageName : Joining an existing overlay Network
