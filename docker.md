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

#### Docker Client

`Docker Client is a tool that we are going to issue commands to.`

#### Docker Server(Docker Daemon)

`Docker Daemon is a tool that is responsible for creating images, running containers, etc.`

#### Getting started

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

  `docker start -a ID-of-Container`

#### 2. List all running containers

`docker ps`

#### 3. List all containers (including shutdown containers)

`docker ps --all`

## 5. Container Lifecycle