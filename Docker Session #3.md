# Docker Session #3



- To understand more about difference between ```CMD``` & ```ENTRYPOINT```:
---------------------------------------------------------------

- Create file.sh script In it:
```
#!/bin/bash
name=$1
job=$2
echo "Hi $name , your job is $job"
```

- Create a dockerfile In it:
```
FROM ubuntu
COPY file.sh /opt/file.sh
RUN chmod 777 /opt/file.sh
WORKDIR /opt
ENTRYPOINT ["./file.sh"]
```

- Build that image
```
docker build -t scriptimg .
```

- Run that image
```
docker container run -it scriptimg
```

- Now O/P is : ```Hi , your job is```
> As ```ENTRYPOINT``` executes a script file, after creating a docker contianer
from that image it will run that script automatically as an enterypoint
& The two parameters are empty (Remember)

- To send parameters you have only two methods:

1- Run image with two parameters
```
docker container run -it scriptimg ahmed SE
```
- Now O/P is : ```Hi ahmed , your job is SE```

2- Now add CMD Line in dockerfile with desired parameters:
```
FROM ubuntu
COPY file.sh /opt/file.sh
RUN chmod 777 /opt/file.sh
WORKDIR /opt
ENTRYPOINT ["./file.sh"]
--> CMD["ahmed","SE"]
```
- Now ```CMD``` acts as the parameter of ```ENTRYPOINT```

- Now Run that image {Without giving any parameters}
```
docker container run -it scriptimg
```
- O/P is : ```Hi ahmed , your job is SE```

- Also Remember you can override ```CMD``` line in dockerfile by adding two parameters
```
docker container run -it scriptimg Arafat CP
```
- O/P is : ```Hi Arafat , your job is CP```

- You can also write
```
FROM ubuntu
COPY file.sh /opt/file.sh
RUN chmod 777 /opt/file.sh
WORKDIR /opt
CMD["./file.sh","ahmed","SE"]
```
- No need for ```ENTRYPOINT``` Line in that case
- Note: in that case you are going to override whole ```CMD``` if you write
a parameter in ```docker container run``` command {Override script also}
while ```ENTRYPOINT``` makes script the enterpoint without overriding it what so ever



Docker Storage:
---------------


1) Bind Mounts :
----------------

- If you have created a docker container, all files and data are non-presentant
which means that if you removed the container all of them will be deleted
This can be solved using [Bind Mounting]

- With bind mount, a file or directory on the host machine is mounted into a container.

- Using the mount flag: (only in Docker 17.06, Not used frequently)
```
docker container run -d --name cont1 --mount type=bind,source=/opt,target=/opt
```
> --mount type=bind,source=<FullPathInHostPC>,target=<FullPathInContainer>


- Inspect the container to find the bind mount:
```
docker container inspect <ContainerID>
```

- Using the volume flag {Famous}
```
docker container run -d --name cont1 -v <FullPathHostPC>:<FullPathContainer> ubuntu:latest
```
> If Directory in docker container does not exist, it will be created automatically



2) Docker Volumes :
------------------


- Use a volume for persistent data:
- Create the volume first, then create your container
- Mounted to a directory in the container
- Data is written to the volume
- Deleting a container does not delete the volume
- Uses the local driver
- Storage locations:
- Linux: ```/var/lib/docker/volumes/<nameOf Volume>/_data```
- Windows: ```C:\ProgramData\Docker\volumes```



- List all volumes on a host PC:
```
docker volume ls
```

- Create two new volumes:
```
docker volume create Vol1
```

```
docker volume create Vol2
```

- Inspect a volume:
```
docker volume inspect <VolumeName>
```

- Deleting a volume:
```
docker volume rm <VolumeName>
```
- Note : In ```Bind Mounts``` method you can simply delete the folder that mount to docker
container folder, but using ```Docker Volumes``` method you can not delete a volume if its
attached to any container


- Removing all unused volumes {Not attached to any container}
```
docker volume prune
```


Docker Network :
---------------

- Docker implements container networking using a framework called the ```Container Networking Model```.

- ```CNM``` utilizes the following concepts:
1. Sandbox
2. Endpoint
3. Network
4. Network Driver
5. IPAM {IP Address Management}


- Containers in same network can communicate with each other

- The Docker native network drivers are part of Docker Engine and dont require any extra modules.
They are invoked and used through standard docker network commands:

1) ```Host``` : Docker container is publicly accessable throw host PC {Quite Bad Practrice As There is no any isolation}
2) ```Bridge``` : Default network driver in docker, containers can communicate with each other {But host PC
cannot acces private IP Address of container}
3) ```Overlay``` : It is used to connect differnet containers in many servers {used in Docker Swarm}
4) ```Macvlan``` : Same as Bridge in addtion to some extra configuraions
5) ```None``` : Container Networking sandbox of container is totally isolated, can not connect to networking
satck of host PC


Networking Commands:
--------------------


- List docker network command menu
```
docker network --help
```

- List all Docker networks on the host
```
docker network ls
```

- Getting detailed info on a network
```
docker network inspect <NetworkName>
```

- List docker network create command menu
```
docker network create --help
```

- Creating a network
```
docker network create <NetworkName>
```

- Deleting a network
```
docker network rm <NetworkName>
```

- Remove all unused networks
```
docker network prune
```

- Adding a container with no network
```
docker container run -d --name <ContainerName> -p 8080:80 <ImageName>
```

- Add the container to a network
```
docker network connect <NetworkName> <ContainerName>
```
> Note: after connecting this container with a new Network, its now connected to both
previous network {bridge by default} + new network

- Remove a container from a network
```
docker network disconnect <NetworkName> <ContainerName>
```

- Create a docker container attaching to a specifc Network
```
docker container run -id --name contnet1 --network <NetworkName> ubuntu
```

- Ensure that the container is connected to that Network
```
docker container inspect <ContainerName>
```
> Note: this container takes IP address from range of created Network in Network Section


- Note: Containers in same network {as Newtork1} will be able to connect with only
containers in same Network as they all in same private subnet


- Now if you created two containers each is connected to different created network {for example : Network1,Network2}
an then enter inside first container {connected to Network1} using ```docker exec``` command,
then run ```ping``` command of IP Address of Network2 {After using ```docker inspect``` command}
- Install ```ping``` command in the container first
```
apt-get update
apt-get install iputils-ping
ping <IPaddressOfNetwork2>
```
- Ping Will not send/recive from that IP Address as each container is in different Network {even if they both in
bridge newtork, but as they are in different network this is because ```Netwrok1,Network2``` each has a different range of IP Address
and they have separated Subnets}


-----


- Three Tiers Application:
    Front-End {Presentaion Layer} -> Back-End {Application Layer} -> Database Layer

     - To Implemet this in world of containers
     - Create frontend/backend/Database each in differnet docker conatiner and establish a network between them

     - How to handle Multi-contianers easily {This is concept of ```Orchestration```}
     - In only appliacion layer it may contains more than 120 microservices {Containers}



Docker Compose :
---------------

- Compose is a tool for defining and running multicontainer Docker applications automatically without
manually writing docker commands
- With Compose, you use a ```.YAML``` file to configure your application’s services.
Then, with a single command you can create and start all the services from your configuration
- Documentation : https://docs.docker.com/compose/compose-file
- You have first to install it

- Note :
- Contianers in Docker Compose will be called services
- While in kuburantes containers are called pods

- A Compose file is called ```docker-compose.yaml```
- If you want to use another name use ```–f <NameOfFile>```
- It is a ```yaml``` file but we have an option to use json as an alternative {Not Recommended}

- Docker-compose file has four types of top-level keys(version-services-networks-volumes):

1) ```Version```: it WAS mandatory, it is to define the compose file format and it is tight to version of docker engine.
2) ```Service```: to define services definition. Each service definition represent a CONTAINER
3) ```Volumes```: to define volumes that will be managed by compose.
4) ```Network```: to define network that will be manged by compose, Bridge is set by default .

- ```Depends_on```: is used to define which service is depend on another one as we can’t run this service before we
making sure the dependent one is running


A ```docker-compose.yml``` looks like this:
```
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

- Note : ```Volumes``` are used in production level instead of ```Bind Mounting```
as you can create a ```volumes``` in server where other devices can access it
while ```mounting``` is used to mount on volumes locally only {On your PC}

- If you have 20 server installed on them docker using swarm you cannot config
20 server with bind mount so you use intead volumes



- To open docker-compose command menu
```
docker-compose --help
```

- Check if there is any synatx error in docker-compose.yaml file
```
docker-compose config
```
- Create a compose services {Containers}:
```
docker-compose up -d
```
> -d : detach {run in background}, as docker-compose up command runs in foreground {holds terminal}

- List containers created by compose:
```
docker-compose ps
```

- Stopping a compose service:
```
docker-compose stop
```

- Starting a compose service:
```
docker-compose start
```

- Restarting a compose service:
```
docker-compose restart
```

- Delete a compose service:
```
docker-compose down
```
> docker-compose .yaml file must be in folder



### Task :


- Create a docker compose file to run WordPress


### Answer :


- Create ```docker-compose.yaml```

- Check for docker and docker compose versions {To be able to write version command in docker compose file}
```
docker --version
docker-compose --version
```
- Dont forget to install docker compose extension from visual studio code
- docker compose is created using python
- So, ```.yaml``` files are like python where extra spaces matter

In ```docker-compose.yaml``` :
```
version: '3.1'

services:

  db:
    image: mysql:5.7        # This Lie is same as $ docker build command
    restart: always         # If container is stopped it keeps restarting
    environment:            # Environment Variables needed to start Database
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:                # Docker Volumes
      - db_data:/var/lib/mysql  # db_data is a volume not a Path

  wordpress:
    depends_on:     # Written In lIne #291
        - db
    image: wordpress:latest
    restart: always
    ports:          # Same As $ docker container run -p 8080:80
      - 8080:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes:
      - wordpress_data:/var/www/html

volumes:
  wordpress_data: {}
  db_data: {}
```
- both are correct, ```wordpress_data: {}``` OR ```wordpress_data:```
but should be applied to all other volumes in docker compose file


Note : ```Evironment Variables``` of wordpress user/name/password is same as database password


- Then Create a compose service:
```
docker-compose up -d
```
- Now open ```localhost:8080``` and Wordpress should works


- To see logs
```
docker-compose logs
```




- Using two FROM in a dockerfile is called ```multi-stage image``` used to minimize layers of image




the most commonly used commands:
-------------------------------
- ```build```: Build or rebuild services
- ```bundle```: Generate a Docker bundle from the Compose file
- ```config```: Validate and view the Compose file
- ```create```: Create services
- ```down```: Stop and remove containers, networks, images, and volumes
- ```events```: Receive real time events from containers
- ```exec```: Execute a command in a running container
- ```help```: Get help on a command
- ```images```: List images
- ```kill```: Kill containers
- ```logs```: View output from containers
- ```pause```: Pause services
- ```port```: Print the public port for a port binding
- ```ps```: List containers


- ```.dockerignore``` : a file used to ignore some files from being coped to an image
as they are useless files to prevent large size of images or these files are senstive
{as ```.gtignore``` in git}



Docker Swarm :
--------------



- As a platform, Docker has revolutionized the manner software was packaged
- Docker Swarm or simply Swarm is an open-source container orchestration platform and is the native clustering engine
for and by Docker. Any software, services, or tools that run with Docker containers run equally well in
Swarm.
- Also, Swarm utilizes the same command line from Docker.
- Swarm turns a pool of Docker hosts into a virtual, single host. Swarm is especially useful for people
who are trying to get comfortable with an orchestrated environment or who need to adhere to a
simple deployment technique but also have more just one cloud environment or one particular
platform to run this on


- Initialize the manager:
```
docker swarm init --advertise-addr <PrivateIP>
```

- Add the worker to the cluster:
```
docker swarm join --token <Token> <PrivateIP>:2377
```

- List the nodes in the swarm:
```
docker node ls
```

- Inspecting a node:
```
docker node inspect <NodeName>
```

- Promoting a worker to a manager:
```
docker node promote <NodeName>
```

- Demoting a manager to a worker:
```
docker node demote <NodeName>
```

- Removing a node form the swarm (node must be demoted first):
```
docker node rm -f <NodeName>
```

- Getting the join-token:
```
docker swarm join-token <Worker|Manager>
```
