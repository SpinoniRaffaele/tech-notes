# DOCS

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications.

Docker provides tooling and a platform to manage the lifecycle of your containers:

* Develop your application and its supporting components using containers.
* The container becomes the unit for distributing and testing your application.
* When you’re ready, deploy your application into your production environment, as a container or an orchestrated service. This works the same whether your production environment is a local data center, a cloud provider, or a hybrid of the two.

Docker is lightweight and fast. It provides a viable, cost-effective alternative to hypervisor-based virtual machines, so you can use more of your server capacity to achieve your business goals. Docker is perfect for high density environments and for small and medium deployments

Docker uses a client-server architecture. The Docker _client_ talks to the Docker _daemon_, which does the heavy lifting of building, running, and distributing your Docker containers. The communications goes with UNIX sockets or a network interface
The docker desktop have both the client and the server, it lets you build and share containerized applications.

A Docker _registry_ stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. You can even run your own private registry.

Image: read-only template for creating docker container: You might create your own images or you might only use those created by others and published in a registry. To build your own image, you create a _Dockerfile_ with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state. Every container is isolated from all other processes on the host machine. That isolation leverages [kernel namespaces and cgroups](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504), features that have been in Linux for a long time  (namespaces create copy of the basic filesystem, croot changes the root of the fs, cgroups is going to limit the resources to the container process - child processes - RAM memery usage - network bandwidth)

When running a container, it uses an isolated filesystem. This custom filesystem is provided by a container image. Since the image contains the container’s filesystem, it must contain everything needed to run an application - all dependencies, configurations, scripts, binaries, etc. The image also contains other configuration for the container, such as environment variables, a default command to run, and other metadata.

A good practice is to put the dockerfile in the root directory of your app, then you can run the following to build the image:
```
docker build -t getting-started .
```

The -t flag tags your image. Think of this simply as a human-readable name for the final image. Since you named the image getting-started, you can refer to that image when you run a container. you can add multiple tag to an image:
```
docker tag IMAGE:latest IMAGE:v1.0.0
```
then you can run 'docker rmi IMAGE:v1.0.0'  to remove only the label and not the image.

The . at the end of the docker build command tells Docker that it should look for the Dockerfile in the current directory.

in order to run your app:
```
docker run -dp 3000:3000 getting-started


```
You use the `-d` flag to run the new container in “detached” mode (in the background). You also use the `-p` flag to create a mapping between the host’s port 3000 to the container’s port 3000. Without the port mapping, you wouldn’t be able to access the application. (by default every post is closed to the outside)

**REMOVE AND UPDATE A CONTAINER**

Remove a container:
in order to remove you first need to stop it
get the runnnig containers using 'docker ps'
Use the 'docker stop <container-id>' command to stop it.

Then you can remove it by using the 'docker rm' command.

If you need to update an app you need to:
remove the old container, rebuild the image with the changes, run the new image.

In docker Hub images are pushed and pulled to/from registries, the default docker registry is the  docker HUB.

In order to push your image you can
check the images:   docker image ls
login to the hub: docker login -u YOUR-USER-NAME
assign your user ID to the image tag: docker tag APP-NAME YOUR-USER-NAME/APP-NAME
push the image: docker push YOUR-USER-NAME/APP-NAME

Every container has its own file space, where it can create files, each container is isolated and cannot see files of other containers also if they have the same image, every time you stop a container you lose the data,
so every time you update your app you will lose the container data

you can run commands inside a running container with:
```
docker exec <container-id> <command-to-run>
```

**VOLUMES**
[Volumes](https://docs.docker.com/storage/volumes/) provide the ability to connect specific filesystem paths of the container back to the host machine. If a directory in the container is mounted, changes in that directory are also seen on the host machine. If we mount that same directory across container restarts, we’d see the same files. (another approach here is to connect remotely to a database which will be another image that already handles persistent storage with volumes  -  redis for example)

Docker handles volumes for you, you only need to specify a name for them.
Imagine you want to persistently store a file:
create a volume in the host machine:
```
docker volume create todo-db
```
restart the image attaching it to the volume with the --mount option:
```
docker run -dp 3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```
the src is the volume name, the target is the path of the folder inside the image
you can directly create the volume in the docker run command for brevity:
```
-v "$(todo-db):/etc/todos"  //the first is the host's volume, the second is the path in the container
```

**BIND MOUNT**
is a type of mount that lats you share with the container a local directory in the host machine. With this YOU choose the folder of the host machine, not docker. You can attach it to a running image with:
```
--mount type=bind,src=/path/to/data,target=/usr/local/data
```

It doesn't copy the content of the container in the host folder, that's a feature that only the named volumes provides

you can use the bind mount to make the container reactive to local development:
```
docker run -dp 3000:3000 \
    -w /app --mount type=bind,src="$(pwd)",target=/app \
    node:18-alpine \
    sh -c "yarn install && yarn run dev" 
```
given that your current directory in the host machine is the root of the app codebase, you are copying the codebase in the container usinf the bind mount. the process runnning inside the container that is reacting to the code changes is the 'yarn run dev'.

**MULTI CONTAINER APPS**
In general, **each container should do one thing and do it well.** A few reasons:

* There’s a good chance you’d have to scale APIs and front-ends differently than databases
* Separate containers let you version and update versions in isolation
* While you may use a container for the database locally, you may want to use a managed service for the database in production. You don’t want to ship your database engine with your app then.
* Running multiple processes will require a process manager (the container only starts one process), which adds complexity to container startup/shutdown

**NETWORK**
Containers in docker can communicate if they are in the same network, each container has its own IP address.
how to create a network:
```
docker network create todo-app
```

How to run an image attached to a network:
```
docker run -d --network NETWORK-NAME --network-alias CONTAINER-HOSTNAME-IN-THE-NET IMAGENAME
```

any other image that needs to comunicate with IMAGENAME, will need to

* be in the same netowork: --network NETWORK-NAME
* target the right hostname (CONTAINER-HOSTNAME-IN-THE-NET) in the application properties (likely to be a secret or env variable)

**DOCKER COMPOSE**
The _big_ advantage of using Compose is you can define your application stack in a file (docker-compose.yml), keep it at the root of your project repo (it’s now version controlled), and easily enable someone else to contribute to your project. The file define how your containers interact and how your app integration is achieved - you need to install docker compose separately.

In the docker-compose you have a set of services (the containers) that define all the thing that they use (like volumes, network, env variable), like with the docker run command, you can specify different options (but with a different syntax).
example:
docker run
```
docker run -dp 3000:3000 \
  -w /app -v "$(pwd):/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:18-alpine \
  sh -c "yarn install && yarn run dev"
```
docker-compose
```
services:
  app:    #app is the name of the container running this image and it is automatically the hostname in the net
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
```

apart form the services you have also the volumes section, which defines all the volumes involved:
```
volumes:
  todo-mysql-data:
```

The network is automatically created by docker for the whole application so you don't need to spacify it (if you don't need more complex handling).
Now you can run the whole stack with a single command:
```
docker compose up -d 
```
and check all the logs generated in a single stream:
```
docker compose logs -f

docker compose logs -f SERVICE-NAME    //outputs only the logs of the specified service
```
in order to shutdown the app run:
```
docker compose down
docker compose down --volumes  //this will also clear the volumes
```

**GOOD PRACTICES**
When you have built an image, it is a good practice to scan it for security vulnerabilities using the `docker scan` command. Docker has partnered with [Snyk](https://snyk.io/) to provide the vulnerability scanning service.

you can also run
```
docker image history IMAGE-NAME
```
to get the history of created layers.

you should create a .dockerignore and ignore there the node\_modules files (in case of a node application)

MULTI STAGE BUILDS
multi-stage builds are an incredibly powerful tool to help use multiple stages to create an image. There are several advantages for them:

* Separate build-time dependencies from runtime dependencies
* Reduce overall image size by shipping _only_ what your app needs to run

in the dockerfile you can specify several 'FROM <imagename>'  commands, each of these command is starting a new stage of the build which is going to be executed in a container based on the image provided in the FROM command.
