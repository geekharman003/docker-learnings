# PART - 1
### problem statement
whenever we create a project on our machine we install certain dependencies like runtime environment, database, os specific libraries.

our project will run fine on our machine, but lets say if someone install your project on their machine then it may not run.why? because it might possible that person has installed some other version of dependencies.or a dependency behave diffrent based on os version.
to solve this issue we use docker.

docker packs our app with all required dependencies in a container.
now no one have to install the dependencies on their machine.
we can simply share the container with everyone.the container will act as virtual machine in which we can install any os and that will on top of our original os.
now their is no need to identify dependency versions.

&ensp;
### daemon vs docker desktop
daemon is responsible for creating,scaling images.
docker desktop is a gui from which we can manage the containers,images.


&ensp;
### image vs container
image - image is a pre-built package that contains everything that is needed to run the app like runtime environment,dependencies,databases etc.each image can contain multiple softwares dependencies.images are downloaded from dockerhub. we can make custom images also.

container - container is just like a machine on which images are run.each container
have their own memory.they don't share their data.


&ensp;
### basic docker commands
```docker container ls``` -> lists all the running containers

```docker container ls -a```-> lists all containers

```docker start <container_name> || <container_id>``` -> start a container

```docker stop <container_name> || <container_id>```-> stop a container

```docker run -it <image_name>``` -> creates a new container and install the image

```docker exec <container_name> <command>``` -> execute a command inside the container and return the result in local machine terminal

```docker exec -it <container_name> bash``` -> execute a command inside the container and keep the terminal control inside container

```docker image ls``` -> lists all installed images


&ensp;
### port mapping
when we run any server inisde the container on any port then by default we can't access the server because container is isolated. my host machine don't know anything about the container.
we have to expose the container port and do the mapping with host machine port so that from host machine we can access the server.

to do that we have to use -p flag. \
e.g ```docker run -it -p port:port <image_name>```


&ensp;
### environment variables
we can pass environment variables to the container \
e.g ```docker run -it -p 3000:3000 -e key=value -e key=value node:latest```


&ensp;
### dockerization of node.js application
to dockerize a node application first we have to create a Dockerfile in project root to define the image configuration. \
then we have to install a base image which will act as a OS on which all other dependencies will be installed.

```
FROM ubuntu

RUN apt-get update
RUN apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_18.x | bash -
RUN apt-get upgrade -y
RUN apt-get install -y nodejs

COPY package.json package.json
COPY package-lock.json package-lock.json
COPY server.js server.js

RUN npm install

ENTRYPOINT [ "node", "server.js" ]
```

then we need to build a image from this configuration using below command: \
```docker build -t <image_name>```
 

&ensp;
### caching layers
dockerfile commands are cached.that means when ever a change happen in any file it will only re-run certain commands not all.


&ensp;
### pushing a custom image
to push a image first we have to create a repo on docker then we can use below command to push image: \
```docker push <image_name>```


&ensp;
### docker compose
when we use multiple services then we have to use multiple containers.running each container manually can be tedious.
to solve this we can use docker compose file which contain all the configurations for running multiple containers.
to start all containers use command:

```docker compose up```

```docker compose up -d``` -> runs the container in background 

```docker compose down``` -> shut down and delete all containers

&ensp;
# PART 2

### docker networking
docker networking is a system that allows multiple containers to communicate with each other or with the host machine.

#### bridge network
 this is the default network driver for containers.
 it creates a bridge b/w the host machine and containers, so containers get the internet access throught that bridge.they can't directly connect with the host machine.
 containers can communicate with each other if they are on the same bridge network.
 we also have to expose the ports so that they can be accessible from host machine.


 \
 to check available network drivers use command: \
 ```docker network ls```

 #### host network
 when container uses host network driver they directly connected with the host machine network.no seperate ip is assigned for containers.

 #### none network
 if container uses none network then they can't able to communicate over the network.none driver disables the networking.

#### custom bridge network
we can create custom bridge network also instead of using default bridge.custom bridge network are useful if we want to run same type of applications on same network.

to create a custom bridge, use command:
```docker network create my-bridge```