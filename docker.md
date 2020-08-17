## 1. Installation

### For Mac users

Download and install Docker for Mac.  
 `https://docs.docker.com/docker-for-mac/install/`

### For Windows users

Windows Home users will not be able install the Docker for Windows Desktop edition, as it requires Hyper-V virtualization. This is supported only by Windows Professional and Enterprise editions.

Windows Home users will need to install Docker Toolbox which uses VirtualBox instead.
`https://docs.docker.com/toolbox/toolbox_install_windows/`

Toolbox will install everything you need including VirtualBox.
You may also need to enable virtualization in your computer's BIOS settings.

### For Linux users

#### Installing Docker

Ubuntu  
 `https://docs.docker.com/install/linux/docker-ce/ubuntu`

Fedora  
 `https://docs.docker.com/install/linux/docker-ce/fedora`

CentOS  
 `https://docs.docker.com/install/linux/docker-ce/centos`

Debian  
 `https://docs.docker.com/install/linux/docker-ce/debian`

#### Installing Docker Compose

Unlike the Mac and Windows Docker Desktop versions, we must manually install Docker Compose.  
 `https://docs.docker.com/compose/install/#install-compose`

## 2. Docker for Windows/Mac

Docker for Windows/Mac consists of Docker Client and Docker Server(Docker Daemon).  
Behind the scenes, Docker for Windows/Mac creates a virtual machine running Linux and provides the ability to run the Docker.

#### Docker Client

`Docker Client is a tool that we are going to issue commands to.`

#### Docker Server(Docker Daemon)

`Docker Daemon is a tool that is responsible for creating images, running containers, etc.`

#### Getting Started

Check docker version.  
`docker version`

Run hello-world.  
`docker run hellow-world`

## 3. Behind the scenes

### Namespacing

Isolating resources per process(or group of processes).

Process, Users, Hard drive, Network, Hostnames, Inter Process Communication etc

### Control Groups (cgroups)

Limit amount of resources used per process.

Memory, CPU Usage, HD I/0, Network Bandwidth

### Docker Image

It contains FS Snapshot, Startup Command

### Docker Container

Running process -> Kernel -> RAM, Network, CPU, Data from FS Snapshot of Image

### 4. Commands

#### 1. Create and Run containers from image

- `docker run` command is the combination of `docker create` and `docker start`.

  `docker run image-name`

- creates docker image and print out ID of container.

  `docker create image-name`

- Now, we can run that container using the ID.  
  -a option helps to show you the output of container to the console.
  Without -a option, we will see only container ID in the console when we use `docker start`.

  `docker start -a ContainerID`

- Running with -a option is expensive. We can easily log out the information by using `docker logs` command after running a container using `docker start ContainerID`.  
  One thing to note is that `docker logs` command doesn't rerun the container.

  `docker logs ContainerID`

#### 2. List all running containers

`docker ps`

#### 3. List all containers (including shutdown containers)

`docker ps --all`

#### 4. Deleting containers

`docker system prune`

This will remove:

- all stopped containers
- all networks not used by at least one container
- all dangling images
- all build cache (Any image fetched from docker hub)

#### 5. Stopping containers

`docker stop ContainerID`

- Stop command will send SIGTERM to the container.
- If the container doesn't automatically stop in 10 seconds then Docker is going to automatically fallback to issuing the `docker kill` command.

#### 6. Killing containers

`docker kill ContainerID`

- `docker kill` command will send SIGKILL to the container.

#### 7. Executing command inside the container

`docker exec -it ContainerID COMMAND`

- -it option allows us to provide input to the container directly from our terminal.
- -it is the combination of `-i` and `-t`. The option `-i` lets us to attach our terminal to the STDIN channel of that running process. The option `-t` is used to show up the text in a nicely formatted manner on the screen.
- COMMAND is the command to execute inside the container.

#### 8. Getting full terminal access of a container using Command Processors (bash, powershell, zsh, sh)

`docker exec -it ContainerID sh`

- To run multiple commands, using `docker exec -it ContainerID COMMAND` for each command is not efficient. So, we can use `sh` to get the terminal access of a container and run the commands inside that container like using a normal terminal.
- Apart from using shell `sh` , there are other command processors like `bash`, `powershell`, `zsh`.

#### 9. Combination of `docker run` and `docker exec -it sh` command

`docker run -it image-name sh`

- Sometimes, we might want to access container terminal just after running the container. In this case, we can use the above shortcut command instead of running the container first with `docker start/run` and then accessing the container terminal seperately with `docker exec -it sh`.
- The downside of using the above command is that chances are you're not going to be running any other process. It's a little bit more common that you're going to want to startup the container with a primary process of like web server and then attached to it a running shell.

## 5. Container Lifecycle

- After starting a container with overwrite command as `docker start ContainerID OVERWRITE-COMMAND`, we can't start it again using another command as it will lead showing the below error.  
  `you cannot start and attach multiple containers at once`

## 6. Creating Docker Image

The image creation flow is like below:  
 `Dockerfile -> Docker Client -> Docker Server -> Usable Image!`

#### Dockerfile

Dockerfile is a configuration to define how our container should behave.

Dockerfile flow:

1. Specify a base image
1. Run some commands to install additional programs
1. Specify a command to run on container startup

#### Creating a redis Dockerfile

Example instructions inside a Dockerfile.

```
# Use an existing docker image as a base
FROM alpine

# Download and install a dependency
RUN apk add --update redis

# Tell the image what to do when it starts as a container
CMD ["redis-server"]
```

Dockerfile format is `INSTRUCTION ARGUMENT` format:

- FROM, RUN, CMD are instructions telling Docker Server what to do using ARGUMENTS such as `alpine`, `apk add --update redis` and `"[redis-server"]`.
- We used `alpine` as base image here because it has a set of programs inside of it that are very useful for installing and running redis.
- We used `apk` and it is preinstalled in `alpine`
- CMD COMMAND isn't executed. the COMMAND is set as the primary command to use everytime when the container runs.

#### Building an image

Inside the directory where `Dockerfile` exists, run the below command to build an image.  
`docker build .`

- Important things to note here is that, docker does cache the image creating process according to the command order in Dockerfile and reuses the cache whenever it is possible. So, changing the order of command in Dockerfile will lead the cache not being reusable. It is recommended to add new command to the very last line of Dockerfile whenever it is possible, so that docker can reuse the cache when building a new image.

#### Building an Image with tag

Running a docker image using containerID everytime is a pain. We can build an image with tag name and run that newly created image using that tag name.

`docker build -t abc/redis:latest .`

- `abc/redis:latest` is the tag name and the convention for it is as below.

  `abc` is Your Docker ID  
  `redis` is Your repo/project name  
  `:latest` is tag version, it is optional and it is the default value if we omit it

#### Taking snapshot image of a running container

`docker commit -c 'CMD ["redis-server"]' RunningContainerID`

- `-c` is used to set `'CMD ["redis-server"]'` as default command in that image.
- after running above command, a newly created image ID will be generated as `sha256: XXXXXXXXXXXX`.
- Run the container by using a newly created image ID with the following command `docker run XXXXX` where we don't need to type full image ID. Docker does know even we type part of image ID if it is unique.

#### Building an image using extra local files

To copy extra local file to the container image, the instruction `COPY` is used.

```
# Specify a base image
FROM node:alpine

# Install some dependencies
RUN npm install

# Copy the local files
COPY ./ ./

# the first `./` is `Path to local folder`
# *relative to build context of `docker build BUILDCONTEXT` command*

# the second `./` is `Place to copy stuff to
# inside *the container*

# Default command
CMD ["npm", "start"]
```

Copying everything to root directory of the container is not ideal. We can set up a work directory when building an image using the instruction `WORKDIR` like below.

```
# Specify a base image
FROM node:alpine

# Any following command will be executed
# relative to this path in the container
WORKDIR /usr/app

# And copy instruction will copy to work directory
# the second `./` is our work directory which is `/usr/app` now!
COPY ./ ./

# Install some dependencies
RUN npm install

# Default command
CMD ["npm", "start"]
```

#### Minizing cache busting and rebuilds for image building

With the above example Dockerfile instructions, if we make a change to the source file which is located in root directory of our project, the following instruction `RUN npm install` will still be executed again because we can't use our build cache.  
To minimize Cache Busting and Rebuilds, we can make a change to how we copy our source files and package.json to the container.

```
FROM node:alpine

WORKDIR /usr/app

# copy only package.json for the following `npm install` command
COPY ./package.json ./

# Install some dependencies
RUN npm install

# After `npm install`, we can copy all files in our root project directory
# so that we don't need to rerun `npm install` everytime we change
# our source files
COPY ./ ./

# Default command
CMD ["npm", "start"]
```

#### Ignoring specific files, folders when building image

To ignore files, we can a file named `.dockerignore` like `.gitignore` and describe the folders and files that we want to ignore inside it.

example content of `.dockerignore` file

```
node_module
```

#### Starting up a container with port mapping

`docker run -p 8080:8080 image-id`

- `-p 8080:8080` is port mapping. the first `8080` is from port of source machine and the second `8080` is the to port inside the container
- After running the above command, we should be able to access the server inside our container from `localhost:8080`. One thing to note here for Docker Toolbox users is that, we can't use `localhost` there, so we would access using `192.168.99.100:8080`.

#### [Delete unnecessary containers and images](https://docs.docker.com/docker-for-mac/space/#delete-unnecessary-containers-and-images)

detailed space usage information by running:  
`docker system df -v`

to list images, run:  
`docker image ls`

to delete specific image, run:
`docker image rm image-name`

to list containers, run:  
`docker container ls -a`

If there are lots of redundant objects, run the command:  
`docker system prune`

This command removes all stopped containers, unused networks, dangling images, and build cache.

It might take a few minutes to reclaim space on the host depending on the format of the disk image file:

If the file is named Docker.raw: space on the host should be reclaimed within a few seconds.
If the file is named Docker.qcow2: space will be freed by a background process after a few minutes.

- Note that many tools report the maximum file size, not the actual file size. To query the actual size of the file on the host from a terminal, run:

```
$ cd ~/Library/Containers/com.docker.docker/Data
$ cd vms/0/data
$ ls -klsh Docker.raw
2333548 -rw-r--r--@ 1 username  staff    64G Dec 13 17:42 Docker.raw
```

In this example, the actual size of the disk is 2333548 KB, whereas the maximum size of the disk is 64 GB.
