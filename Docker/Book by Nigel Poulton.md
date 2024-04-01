The docker engine is the core component of docker that runs container, it can be downloaded for windows and linux
OCI: open container initiative: governance council responsible for standardizing the fundamental component container infrastructure such as image format and container runtime (it publishes specs: the image spec and the runtime spec)

Components that make up Docker engine:
- client, 
- daemon,
- containerd, 
- runc, 
- libcontainer

Libcontainer is the tool that docker uses to access the OS building blocks used to create and manage containers.
Runc is the component responsible for creating containers, it uses libcontainer underneath.
Containerd is the supervisor of the container lifacycle, it manages operations as starting, stopping, pausing, un-pausing and destroying containers.
The client is installed in different machine and its purpose is to convert CLI commands into POST requests to the docker daemon.
The daemon is implementing the REST API interface without actually performing any container related operation, it delegates these operations to containerd. The main logic in the daemon is: image management, image builds, authentication, security and orchestration.

IMAGES
images are a stack of read-only layers, at the bottom there is a cut-down (minimal) OS and other dependencies needed are added on top. Images don't contain a kernel, they rely on the kernel of the host, we say that they contain 'just enough operatin system'. They are really light (ex: Alpine Linux docker image -> 4MB size)

The images are stored in docker registries, every company can create its own registry, each registry contains multiple image repositories and each repository contains multiple images.
Repository can be official (docker analyze and approve images) or unofficial (wild west).
When pulling (docker image pull) docker assumes that you are referring to the official repository (dockerhub), if you want to specify another one you need to be explicit.
`docker image pull REPOSITORY/IMAGENAME:IMAGETAG`
notice that the 'latest' image tag is not guaranteed to point to the newest image in a repository (each image can be tagged latest).

`docker inspect IMAGENAME` 
give you information about the image, it shows also the layers that compose it, images share layers and this lead to optimization of what needs to be downloaded. Each image is referred to by an hash computed starting from the content of the image (so this is the true identifier and not the tag). Each layer is also identified by a hash called CryptoID.
Each image also has a 'Fat manifest' which is a file that lists the architectures supported by the image, the 'image manifests' instead lists the layers that compose the image.

VM MODEL
running multiple VMs on a server needs multiple OSs running as well, each OS consumes resources and it is a potential attack surface, it needs to be updated and patched, this is called OS tax. The container model uses one single kernel and only that one needs resources and needs to be patched/upgraded. A container start up much faster than a VM no need for kernel operation that search for hardware and initialize them (containers can start in less than a second).

CONTAINER
runtime instances of images. the container is alive until the command that it is executing stops.
Stopping a container does not remove the data inside it. With the docker rm command you loose the data as well. (use docker stop before so that the container can clean up properly and exit gently)
`docker container run -dp HOSTPORT:CONTAINERPORT CONTAINERNAME`
running a process inside the container: `docker container exec CONTAINERNAME COMMAND`

DOCKERFILE
it needs to be in the root dir of the project, it needs to start with a capital D. The docker file is also a documentation file bridging the gap between devs and ops teams.

Is the file telling which instructions will be executed during the docker build
You need it in order to create your own docker image
Example of dockerfile:
	specify an image as starting point
	FROM ubuntu  
	specify a completely empty image
	FROM SCRATCH
	set the person which maintain it
	MAINTAINER name surname
	label
	LABEL mylabel
	run commands on the image (executed only during the build of the image)
	RUN apt-get update
	run commands executed when you create a container from the image
	CMD \["apt-get", "update"\]

Not all the commands in the dockerfile adds layers, FROM, RUN, COPY add layers.
COPY is copying from the host to the container.
WORKDIR is used to set the directory on which next commands will be executed.
ENV sets environment variables.
Generally if a command is changing the filesystem of the image (adding removing changing files), it will create a new layer. The way in which you write the dockerfile has huge impact on the size of the final image and the amount of layer created, in order to avoid shipping big images docker developed MULTI-STAGED BUILDS.

MULTI STAGE BUILDS
multi-stage builds are an incredibly powerful tool to help use multiple stages to create an image. There are several advantages for them:

* Separate build-time dependencies from runtime dependencies
* Reduce overall image size by shipping _only_ what your app needs to run

in the dockerfile you can specify several 'FROM IMAGENAME'  commands, each of these command is starting a new stage of the build which is going to be executed in a container based on the image provided in the FROM command.
Then when you need to ship, you only ship the last image, discarding the ones used to build it.
Ex:

```dockerfile
FROM maven as maven-builder
COPY . .
RUN mvn clean install -DskipTests
FROM tomcat
ENV MONGO_HOST=mongo
COPY --from=maven-builder target/*.war /usr/local/tomcat/webapps/
EXPOSE 8080
CMD ["catalina.sh", "run"]
```

notice the COPY --from command that specify to copy something from another build stage.

DOCKER SWARM
A swarm is a cluster of Docker hosts organically cooperating orchestrated by docker. (it's an alternative to kubernetes).
In order to create a swarm all the nodes  needs to be albe to communicate to each other ina network. Nodes can be
- manager: look after the state of the workers, they are in charge of dispatching tasks (containers), they keep track of the stat of the swarm (with a DB distributed over the managers). Managers also handle the security, they authenticate nodes setup TLS and perform periodic key rotation.
- workers: they accept tasks from the managers and execute them.
The way to run tasks are services, Services are wrapper for tasks (container running), when you want to scale the amount of container running you scale the service.

`docker swarm init --advertise-addr IP --listen-addr: IP`: creates a swarm an set the current node as a manager node. It's good practice to explicitly set the IP of the host in the command, the other nodes will use this IP to connect to it. Listen address is the address from which we will receive swarm traffic.
`docker node ls` you list the nodes in the swarm.

To join other nodes you need to generate tokens (one per type of node):
`docker swarm join-tocken manager`
`docker swarm join-tocken worker`

then from the other host, you can run:
`docker swarm join --token TOKEN`

Notice that the token is all you need to add nodes to the swarm, so it needs to be kept safe, you can also rotate it:
`docker swarm join-token --rotate manager`
`docker swarm join-token --rotate worker`

A swarm should have multiple managers, but only one at a time is active and sends messages to the workers. Workers needs to reach consensus, so a best practice is to create an odd and small number of manager (3 or 5).
Create a service from a manager node:
`docker create service --name frontend -p 8080:8080 replicas 5 IMAGENAME`

The swarm will try to balance the number of containers runned by each workers only considering the number of container that each node runs (not considering the difference of loads between the containers). The managers run constantly a reconciliation loop that checks if the state of the swarm is up to date with the desired state and act accordingly.
you can inspect a service running in a swarm: `docker service inspect SERVICENAME`
Scaling the service: `docker service scale SERVICENAME=10`
In order to upgrade the image that the service is runnning you can use rolling upgrade:
`docker service update --image NEWIMAGENAME --update-parallelism 2 --update-delay 20s SERVICENAME`
it will update two instances (containers) at a time with intervales of 20s between them.

NETWORK
`docker network create -d overlay NETWORKNAME` overlay is the name of the driver used, this is the most popular.
an overlay network is a layer2 network that hides the fact the nodes are into different networks (it's like a VLAN). When executin other docker command you can specify the network by using 
`--network NETWORKNAME`
When a swarm starts a container on an overlay network it extends that network to the node the container is running on. The docker network assigns new IP to the host inside it so you will need to inspect the network to see the actual IP assigned to the hosts.

SECURITY
The swarm mode is secure by default, you will get automatically:
- cryptgraphic node ID,
- authentication
- CA configuration (the root is the manager that creates the swarm)
- CA rotation (default each 90 days override with: `docker swarm update --cert-expiry 720h`)
- encrypted networks

Docker Content Trust is a docker security feature that let's you sign your images and verify the integrity of the images that you pull. not abled, enable with `DOCKER_CONTENT_TRUST=1`

Docker Security Scanning is a tool that can be used to scan images at binary level and detect known vulnerabilities.

Docker secrets are stored in encrypted cluster store across the managers, they are encrypted in-flight and at-rest. Cluster store is based on etcd and its automatically replicated across managers.
They are sent only to the containers need them and after the container completes the secred is wiped from its memory.

Kernel namespaces: they are the heart of containers: they slice logic resources of a kernel:
- network namespace - each with its IP and a full set of ports (isolated network stack)
- mount namespace - each container has an isolated copy of the filesystem with its own root
- process namespace -set of PID,
- user namespace - set of users inside the container mapped to host users
- inter-process-communication namespace: used for shared memory access

A container from a lower level perspective is just a collection of kernel namespaces.

Containers are isolated from each other but they share physical OS resources (CPU, RAM, disk IO, network IO). CGroups are kernel capabilities used by containers to limit the resources that they can use.