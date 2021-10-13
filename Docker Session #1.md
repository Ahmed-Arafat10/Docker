==========
Session #1
==========

Intro :
-------

- Deployment : its stage in which developer host there application so that end user can use that application

- A Process : Unit of resources allocated

- Before Virtualization :

Problems were:
1) Conflict between different applications as everyone needs a different dependencies, where
server can only contains one application running, so if you want to add another application with different
dependencies you need another server
2) Resources are not efficiently allocated, as you have to buy a server with huge specifications
{Which is too costly for new businesses} but during early age of project just few people will use
your project

            Application
            -----------
          Operating system
    ---------------------------
    Hardware{Physical Resources}


- After Virtualization : The process of running a virtual instance of a computer system in a
layer abstracted from actual hardware


    VM1|VM2|VM3|
    ------------ Other Application
    Hypervisor |
    ----------------------------
       Host Operating system
    ----------------------------
    Hardware{Physical Resources}


- Virtual Machine :

       App1  |   App2  |   App3
    Bins/Libs|Bins/Libs|Bins/Libs
    Guest OS | Guest OS | Guest OS
    -----------------------------
             Hypervisor
    ----------------------------
       Host Operating system
    ----------------------------
    Hardware{Physical Resources}

- Problem of virtualization is that it needs too much resources, for example
if you have to deploy 3 applications each one needs Linux OS but with different dependences & binaries
you then will create 3 virtual machines, each one has a Linux OS installed in it {Too much redundancy}
+ required dependences & binaries for that application
where only different between those 3 application is that they have different dependences but with same OS



- Why Linux is secure more than Windows ?
- Answer: chroot feature in Linux, which means that each process in the system create a user of
process name {Process A,B,C create Users A,B,C respectively}, each user runs and have full access on its
process {User A runs Process A} and have all privilege to access its process + child processes of that
process , but its isolated from other process {Have no privilege on them},so each process has its own
root user while system root user have privilege on all of them this means that even if a hacker accessed
a process A from unsecured port, all he can do is to destroy that port without being able to access other
Process like B,C or even system root user as User A has only privilege on Process A



Docker :
--------
- Docker is a tool designed to make it easier to create, deploy, and run applications by using containers.

- Docker containers allow a developer to package up an application with all of the parts it needs, such
as libraries and other dependencies, and ship it all out as one package.

- Docker is fast. Very fast. While a VM can take an at least a few minutes to boot and be dev-ready, it
takes anywhere from a few milliseconds to (at most) a few seconds to start a Docker container from a
container image.


- List docker commands
$ docker --help

- To List docker container commands
$ docker container --help


- To List docker run container command
$ docker container run --help


- You can not share docker containers as they are running proccesses in the system, you only can share images
in which docker container is created from

Docker Images:
--------------

- A Docker image is made up of multiple layers. A user composes
each Docker image to include system libraries, tools, and other files
and dependencies for the executable code.

- Docker users store images in private or public repositories, and
from there can deploy containers, test images and share them.

- Docker offers Docker Hub, which is a cloud-based registry service
that includes private and public image repositories.

- Any name of image in docker hub consists of two parts -> RepositoryName:TagName
- TagName of an image is unique name that identifies an image
- Example -> ubuntu:latest


- To install an Image from Docker Hub {With latest tag by default}
$ docker pull <ImageName>


- To install an Image from Docker Hub {With specific tag}
$ docker pull <ImangeName:Tag>



- Create a container in foreground
$ docker container run --name firstcont -it ubuntu:latest
> -i : interactive
> -t : tty {show result in terminal}
> --name : to give the container a name
> Note : Each container must have a unique name
-> If you do not give a container a name, system will give it any name and system will show
ID of created container after execution of command
-> You can control name but ID will be automatically allocated to created container


- Create a container in background
$ docker container run --name secondcont -id ubuntu:latest
> -d : stands for detach, run container in background


- To exit from container terminal without stopping the container
press -> ctrl + p + q


- List created Docker Containers
$ docker ps
> Shows Only running docker containers

$ docker ps -a
> Shows ALL docker containers {running or stopped}


- List running docker containers {Docker New Version}
$ docker container ls


- List all docker containers {Docker New Version}
$ docker container ls -a


- Stop a docker container
$ docker container stop <ContainerID>
> OR <ContainerName>
> You can use just first two character of ContainerID instead of copying whole ID

- Delete all stopped docker containers
$ docker container prune


- Remove all containers whether running or stopped {Not Recommended At All}
$ docker rm -f $(docker ps -a -q)


- List docker container exec command menu
$ docker container exec --help


- Run a command in terminal of a running docker container
$ docker container exec -it <ContainerID> ls /


- Open a bash shell in a running docker container {Enter inside a running container}
$ docker container exec -it <ContainerID> bash


Note: even if you used one image in many containers, each container have its own
data {Isolation}

- Container : it is a running instance of an image


- List images in system
$ docker image ls


- Docker uses concept of LFS -> Layer File System
- Image contains of multiple layers
- Each container share same image layer, and have its
own R/W Layer {Read/Write} in which all of its files/Directories are exist in


- Start a stopped docker container
$ docker container start <ContainerID>


Create Costume Image :
---------------------

- To create our own image we can do one of the following:
 1) Create a container from your desired image, add and edit {Not Recommended At All}
    - whatever you want then commit all these changes to an image.
    - docker commit CONTAINER_ID new_image_name
 2) Use Dockerfile.


- List docker commit command menu
$ docker commit --help


- Create an image from RUNNING container (This image will contains all changes in that containers that
will be used to create another container with same data {Files/Dir.})
$ docker commit <ContainerID> <NewImageName:tag>
> will have [:latest] tag


- New tag means a new image even if repository name is same
$ docker commit firstcon firstimage:v1


- If you created an image with same name:tag that is already exist, created  image will override the exist image
while in creating a container terminal will shows an error that this name is already exists


- CI -> Continuous Integration
- CD -> Continuous Development

- Show layers of an Image {Based on docker}
$ docker image history <ImageName>
Example : $ docker image history httpd


- If you used history command with an image that is created by $ docker commit command in Line #211
you wont be able to see all commands you entered in bash terminal, instead you will only have a line
that tells you that there was an activity in bash with size of executed command without telling you
what exactly you wrote, so we use second method using dockerfile as it shows exactly what created
image is consists of

- While history command Show layers of an Image that are created using dockerfile


Docker File :
-------------

- Dockerfiles are instructions. They contains all of the commands used to
build an image.

- Any image that will be created must be created from a base Image

- Any docker file must begin with [FROM] statement that sets base image

- You can use two [FROM] statements in a docker file but this is an advanced type which is called
multi-stagel image {Not covered in the course}

- To be continued in #2 Session
