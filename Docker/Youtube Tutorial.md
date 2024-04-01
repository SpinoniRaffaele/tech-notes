# Youtube Tutorial

Docker work at deployment stage
It is a software container platform
When deploying you are involving backend, frontend, DB, external libs…
All this can be deployed on different environments (different OS, different systems, even different browsers)
Containers are standard boxes (running environment) where the application is deployed, having a container allows us to test the application only in that environment and deploy it safely in more envs.
In this way a dev only care about software and not about different deployments environment.

What is the difference between it and virtual machine? In containerization we are not creating a new OS inside the base OS but only a memory area that run an application.

Docker is a client-server, the client is the CLI that send API req to the docker daemon (server) that can be in a different or same machine

With docker you run all your programs on isolated containers, so every program can use its own version of every specific dependecy. docker can run any OS in their container, the requirement is that the **kernel is the same.  you cannot run a windows docker image over  linux server.**

Docker allows you to not worry about portability
In docker containers are isolated between them and with the OS

an image is a template from which you can instantiate containers and run them isolately

In docker hub you can see repositories and here you can see how to pull them locally

Requirements: 64bit machine

COMMANDS (linux):
Packet manager install docker
get info on docker: docker info
Start: sudo service docker start
Login (with a docker ID): docker login
Get containers: docker ps -a
Get images: docker images    (use -f "dangling=false"  to retrieve the non running images, use -t TAG to tag the image)
Pull images from repos: docker pull
Remove images: docker rmi
Run an image: docker run IMAGENAME
Run and enter in an image: docker run -it IMAGENAME
Delete stopped container WARNING: docker system prune
Check system volume used: docker system df

INSTALL(windows):
Edit [start.sh](http://start.sh) file (search online)
Start the docker CLI

|     |     |
| --- | --- |
| Docker images are   templates about what is inside the container - logical entity - create with   dockerfile - not running |     |
| The container can   be seen as an instance of the image - real entity - create with "docker   build"  - running |     |

DOCKER ARCH
![[./_resources/Youtube_Tutorial.resources/Untitled.png]]
Here docker client and docker host are both on your machine
For the run you don't need a container, it can be built at the moment, in fact containers are running images
You can start/pause images, it will stop the execution (docker pause CONTAINERNAME)
You can get the stats of your running container (docker stats)
You can stop a container with docker kill CONTAINERNAME and the docker rm CONTAINERNAME

How to run a Jenkins server on docker
There is already a jenkins image called "jenkins"
Then you set up a server , an admin user and there you can create jobs



Docker COMPOSE
Used to define multi container applications (in particular Microservices)
We can start all the containers in one command - docker compose up (down to stop them)
The configuration of your multi container Is inside docker-compose.yml
Example of file content:
		Version: '3'
		Services:
				Web:
						Image: nginx
				imageOfDB:
						Image: redis
In general this file is more verbose, it exposes ports and configures volumes
You can check the validity of the file : docker-compose config
Than you can start all with : docker-compose up
You can scale the containers for services:
Docker-compose up -d --scale NAME-OF-THE-SERVICE-TO-SCALE

VOLUMES

Volumes are for persistent data used by docker containers, they are sherable between containers
Docker volume (here you can see the sub commands, like: create, ls, rm, prune (remove all the volumes)
When you make a "docker run" you can add an option to target a volume, -v VOLUMENAME:/PATHFROMCONTAINER
This will put the path from the container into the volume specified
If I do this for more images, the volume will be shared
Instead of creating a logical volume you can use also physical one,
