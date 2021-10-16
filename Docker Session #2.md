# Docker Session #2


- cgroup Feature In Linux: stands for control group in which each process has a limit
to use resources
- Two terms are well known are request/limit of resource
- For example : process A has limit of 1 GB of ram
Process A requested 500 mb of ram {cannot exceed limit}

- Namespaces Feature: Each process in Linux act as its alone in the
system {Totally Isolated} as its the system, so process are put in
a virtual isolated area along with needed resources


Docker file instructions :
---------------------------

- ```FROM```: Initializes a new build stage and sets the Base Image
- ```RUN```: Will execute any commands in a new layer
- ```CMD```: Provides a default for an executing container. There can only be one
CMD instruction in a Dockerfile
- Remember if you write a multi CMD in dockerfile,
last CMD will override all of them {it overrides also CMD of base image}
also in ```docker container run -it --name test Ubuntu ls /```
> ```ls /``` : will override CMD of Ubuntu image {```CMD["bash"]```} so it will execute ```ls``` command
then will exit from container {As bash is what make a container up and running}
- ```LABEL```: Adds metadata to an image
- ```EXPOSE```: Informs Docker that the container listens on the specified network ports at runtime
- ```ENV```: Sets the environment variable <key> to the value <value>
- ```ADD```: Copies new files, directories or remote file URLs from <src> and adds them to the file system of the image at the path <dest>.
- ```COPY```: Copies new files or directories from <src> and adds them to the file system of the container at the path <dest>.
- ```ENTRYPOINT```: Allows for configuring a container that will run as an executable
- Remember : you can override enterypoint by ```--entrypoint``` in ```docker container run``` command {New feature in Docker}
- ```VOLUME```: Creates a mount point with the specified name and marks it as holding externally mounted volumes from native host or other containers


- ```USER```: Sets the user name (or UID) and optionally the user group (or GID) to use when running the image and for any RUN, CMD,
and ENTRYPOINT instructions that follow it in the Dockerfile
- ```WORKDIR```: Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow it in the Dockerfile
- ```ARG```: Defines a variable that users can pass at build-time to the builder with the docker build command, using the --build-arg <varname>=<value> flag
- ```ONBUILD```: Adds a trigger instruction to the image that will be executed at a later time, when the image is used as the base for another build
- ```HEALTHCHECK```: Tells Docker how to test a container to check that it is still working
- ```SHELL```: Allows the default shell used for the shell form of commands to be overridden

- Note : $ docker container run --name container1 -it Ubuntu ls /
> ls / will override CMD of Ubuntu Image
- ```CMD``` can be overwritten while ```ENTRYPOINT``` cannot , so enterpoint {Bash script} instruction is put inside ```ENTRYPOINT```
but ```ENTRYPOINT``` can be overwritten also by adding flag ```--entrypoint```



Build an Image using Dockerfile:
--------------------------------

- Enter Visual Studio code
```
code .
```

- Build an Image using Dockerfile
```
docker build -t <NewImageName> .
```
>[.] automatically use file of name [dockerfile] in current directory


- Build an Image using MODIFIED Dockerfile name {Bad Practice}
```
docker build -f <ModifiedDockerFileName> -t <NewImageName>
```

-----
Task :
-----

- Run httpd container modify its configuration file to run on port ```9090```
instead of port ```8080```, then echo a text when docker container starts running

Answer :
-------

- First you have to have a configuration file that is edited to port ```9090```, but you dont
have this file, so you will run a httpd docker container and then copy configuration file
into your host PC

- To customize the configuration of the httpd server, first obtain the upstream default configuration from the container:
```
docker run --rm httpd:2.4 cat /usr/local/apache2/conf/httpd.conf > httpd.conf
```
> --rm : automatically remove container when it exits, as we overwritten default
CMD["bash"] then container will exit after executing ```cat``` command since ```--rm```
flag is used, so docker container will be automatically removed
  
- Now you have the configuration file in your host PC just use ```nano``` command and edit [```listen 80 to 9090```]

- Now you can copy the modfied configuration file into path of configuration configuration file
inside image
- You can then ```COPY``` your custom configuration in as ```/usr/local/apache2/conf/httpd.conf``` :

- In Dockerfile :
```
FROM httpd:2.4
COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
CMD "Hello World"; httpd-foreground
EXPOSE 9090
```

- Note: We add [```httpd-foreground```] in CMD Line, as CMD Line will override
Default CMD of image http:24 which runs httpd in foreground {Hold Terminal}
,so without adding this command in CMD, httpd wont be run

- In ```EXPOSE``` Line, we told the container that it allow requests on port 9090
as httpd is running inside a docker container having port 9090


- You can run docker commands in a sandbox environment {Just For Testing}
website : https://labs.play-with-docker.com/
> **Dont use your credentials**


- Remove a container
```
docker container rm <ContainerID>
```

- Build an Image with tag using Dockerfile
```
docker build -t ImageName:Tag .
```
  
- Note: If you are going to build an image from a dockerfile, then you try to
build another image from that dockerfile, docker demoe will use layers of
first image in second image this is called cache. This is done to prevent time
and storage consumption, to prevent docker from using previous same layers will building
an image use ```--no-cache``` option .


- Build an Image with tag using Dockerfile without caching from previous images
```
docker build -t ImageName:Tag --no-cache .
```

- Map to port of docker container {5050 refers to 9090}
> This means that ```localhost:5050``` in Host PC will be able to communicate
with port ```9090``` of docker container
```
docker container run -it -p 5050:9090 <ImageName>
```

- VIP Note: if you use ```-p``` in ```docker container run``` coomand, you dont have to write
```EXPOSE 9090``` in Dockrfile, this is for sure if you want to access container
form localhost, if there is any other container that needs to access this container
you then have to use ```EXPOSE``` in dockerfile
{Best Practice is to write EXPOSE anyway, so team can understand what you did}


  
Docker Registries (Docker Hub) :
--------------------------------
  
- Login into your Docker Hub Account
```
docker login -u DockerHubID
```
> Then enter your Account password

- Give an image a prefix of your Account name in Docker Hub {to be able to push it}
```
docker tag myimg:v1 ahmedaraft10/myimg:v1
```
  
- Push an image into your docker hub account
```
docker push ahmedaraft10/myimg:v1
```
> image name should have prefix which is your Account Name in Docker Hub


- Download an image from you Docker Hub Account
```
docker pull ahmedaraft10/myimg:v1
```


- Remove an image from your Docker Hub Account
```
docker image rm ahmedaraft10/myimg:v1
```
  
- Remove an image from your host PC
```
docker image rm Ubuntu
```
  
- Note: Best practice is that instead of writing too many ```RUN``` command in
a dockerfile, just create one ```RUN``` and then put ```&&```OR ```;``` between differnet Linux commands
this is done as each ```RUN``` is created in a separate layer which is not a good thing.
