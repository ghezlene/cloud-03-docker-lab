# Docker architecture 

> Understanding how docker works internally, how it's components interact, and how containers are created, executed and managed. 

## 1. Introduction 

Docker is a containerization platform. 

It allows us to package an application and its dependencies into an isolated environment called a **container**.

For example, for an application that requires : Java 21, spring boot, postgresql ... 
Without docker we'd need to install and configure these dependencies manually on every machine. 
With docker, we can package the application environment into images and use the latter to create containers. 

The same image can run on different machines that support Docker. 

An image is a template and a container is an instance created from that template. 

## 2. Docker Architecture Overview 

Docker uses a client-server architecture. 
The client sends instructions, the daemon manages docker resources, and lower level components handle container execution. 

### 2.1 Architecture diagram 

### 2.2 Main components 
| Component | Role |
|---|---|
| Docker Client | Receives commands from the user. |
| Docker Daemon | Manages Docker resources and communicates with lower-level components. |
| Docker API | Allows the client and other tools to communicate with Docker. |
| Docker Engine | The overall Docker platform and its core execution services. |
| containerd | Manages container lifecycle operations. |
| runc | Creates and starts containers using OCI specifications. |
| Image | Template used to create containers. |
| Container | Isolated application process and its environment. |
| Network | Allows communication between containers and other systems. |
| Volume | Stores persistent data. |
| Registry | Stores and distributes images. |

# 3. Docker client 
### 3.1 Definition 

The docker client is the interface used by users to interact with docker. 

The most common client is Docker CLI (Docker Command Line interface) 

It allows the execution of commands such as:

```bash
docker run nginx
docker ps
docker images
docker stop container_name or container_id 
```

The client sends requests to the Daemon through Docker API  to create or execute containers. 

### 3.2 Notes: 
- The CLI can communicate with a **local** or **remote** docker daemon 
- It uses Docker contexs to select which doesmon to communicate with 
- Other clients can communicate with docker without using the CLI 

## Docker Daemon (dockerd) 

### 4.1 Definition 

The docker daomon is a background service responsible for managing Docker objects. 
It's process is called **dockerd**

It runs continuously and listens for API requests. 

It manages: 

- Container creation and management 
- Image building and management 
- Network creation and management 
- Volume creation and management 
- Communication with registries 
- Container lifecycle operations 
- Coordination with containerd 


### 4.2 Docker daemon communication 

The docker CLI communicates with the daomon through a Unix socket (/var/run/docker.sock). It's a communication endpoint. 
The daemon can expose a HTTP API over TCP when configured. 
The access to the socket is privileged  and a user with access can often obtain root-level control over the host. 

## 5. Docker REST API 

## 5.1  Definition 

The docker API is the communication interface between docker clients and the daemon. 
It allows clients to send requests and receive responses.  It uses HTTP-based communication 

### 5.2 Example

The following CLI command:

```bash
docker ps
```

causes the client to request container information from the daemon.

Conceptually, this corresponds to an API request similar to:

```http
GET /containers/json
```

The REST API allows CLI communication, IDE integrations, Automation scripts, CI/CD tools, Remote docker management, Custom applications that interract with docker. 

## 6. Docker engine 
### 6.1 Definition 
Docker engine is the core technology used to build and run containers. 

Components: Docker CLI, Docker daemon, Docker API , containerd and associated runtime components. 

### 6.2 Responsabilities
- build images 
- run containers 
- Managing networks
- Managing volumes 
- Handling container lifecycle 

### 6.3 Docker Engine vs Docker CLI 

| Docker CLI | Docker Engine |
|---|---|
| User interface | Containerization platform |
| Sends commands | Performs Docker operations |
| Communicates with daemon | Includes the daemon and core execution components |
| Example: `docker ps` | Example: creating and managing containers |

## 7. containerd 

### 7.1 Definition 
Containerd is a container runtime daemon responsible for managing the container lifecycle. 
It is used by docker engine to handle lower-lovel container operations

### 7.2 responsabilities 

- managing container lifecycle
- managing container images and snapshots
- Coordinating container execution 
- managing runtime operations 
- supervising container processes 


### 7.3 Its position in the architecture

```text
Docker CLI
    |
    v
Docker Daemon
    |
    v
containerd
    |
    v
containerd-shim
    |
    v
runc
    |
    v
Container
```
the shim helps manage the container process independently of the containerd daemon. 
Restarting containerd does not necessarily mean that every running container must stop. 

### why separate containerd ? 

Separating containerd lifecycle management from the docker daemon makes the architecture more modular. 

##runc 
### 8.1 Definition 

runc is a low-level container runtime that creates and starts containers to the Open Container initiative (OCI) specifications. 

It works under containerd 

### 8.2 Responsibilities 

- creating container processes 
- setting up namespaces 
- Applying cgroup restrictions 
- Configuring the container environment 
- starting the container process 

## 8.3 Role 

- Docker doemon : Coordinates docker operations 
- containerd : manages container lifecycle 
- runc : creates and starts the actual isolated container process 

## 9. Docker images 

## 9.1 Definition 

A docker image is a read-only template used to create containers 
It contains the filesystem layers and metadata required to run an application. 

### Image structure 

Example:

```text
       Docker Image
    +------------------+
    | Application Code |
    +------------------+
    | Dependencies     |
    +------------------+
    | Runtime          |
    +------------------+
    | Base Image       |
    +------------------+
```
Each layer represents filesystem changes. 
Layers can be reused between images, reducing storage and build time 

### 9.3 Image characteristics 

- Immutable after creation 
- can contain multiple layers 
- can be tagged 
- can be stored in regestries 
- multiple containers can be created from the same image 

### 9.4 Image vs Container

| Image | Container |
|---|---|
| Template | Instance |
| Read-only layers | Read-only layers plus writable container layer |
| Stored locally or in registries | Created and managed by Docker |
| Can create multiple containers | Represents an individual container |



## 10. Docker containers 
### 10.1 Definition 
A container is an isolated process running from an image 

It includes: 
- Application process
- Filesystem view 
- Environment variables 
- Network configuration 
- Runtime configuration 

## 10.2 Container filesystem 
When a container is created, docker adds a writable layer on top of the image's read-only layers 


```text
        Container
    +-------------------+
    | Writable Layer    |
    +-------------------+
    | Image Layer 3     |
    +-------------------+
    | Image Layer 2     |
    +-------------------+
    | Image Layer 1     |
    +-------------------+
```

Changes made inside the container are generally stored in the writable layer.

If the container is deleted, this writable layer is deleted too.

This is why persistent data should be stored using volumes or bind mounts.


### 10.3 Container lifecycle 

```text
Created
   |
   v
Running
   |
   +-------> Paused
   |            |
   |            v
   |          Running
   |
   v
Stopped
   |
   v
Removed
```

A container can be restarted after stopping, provided it has not been removed.

## 11. Docker networks 

Docker networking enables containers to communicate with: 
- other containers 
- the docker host 
- External networks 
- Internal networks 

### 11.2 Network architecture 

```text
             Host Machine
                  |
           Docker Network
                  |
        +---------+---------+
        |                   |
        v                   v
  +-----------+       +-----------+
  | Container |       | Container |
  |    A      |       |    B      |
  +-----------+       +-----------+
        |                   |
        +-------------------+
          Internal traffic
```

### 11.3 Main network drivers 

| Driver | Purpose |
|---|---|
| bridge | Connects containers on a host. |
| host | Shares the host network namespace. |
| none | Disables normal container networking. |
| overlay | Connects containers across multiple Docker hosts. |
| macvlan | Gives containers their own MAC addresses on a physical network. |

### 11.4 Example

Imagine a web application and a database.

```text
        Browser
           |
           v
       Web Server
       Container
           |
           v
       Docker Network
           |
           v
        Database
        Container
```

The web application communicates with the database through the Docker network.

## 12. Docker volumes 
A docker volume is a persistent storage mechanism managed by Docker.
It allows data to survive container deletion and recreation 

### 12.2 Why are volumes important? 
Containers are designed to be replaceable and their writable filesystem layer is not a reliable place for important persistent data. 

example: a database should preserve it's data even if the container is removed. 


### 12.3 Volume architecture

```text
      PostgreSQL Container
               |
               | Mount
               v
         Docker Volume
               |
               v
         Persistent Data
```

### 12.4 storage types 

| Type | Description |
|---|---|
| Volume | Storage managed by Docker. |
| Bind Mount | Maps a host filesystem path into a container. |
| tmpfs | Stores temporary data in memory on Linux. |

## 13. Docker registries 

A docker registry is a service that stores and distributes docker images 
The most common public registry is docker Hub
### 13.2 How it works

```text
Developer Machine
       |
       | docker push
       v
Docker Registry
       |
       | docker pull
       v
Production Server
       |
       v
Container
```

### 13.3 Main operations

- Pull: Download an image.
- Push: Upload an image.
- Tag: Assign a name or version to an image.
- Login: Authenticate with a registry.

### 13.4 Why registries matter

They allow developers to build images once and distribute them to different environments.

This is especially important in CI/CD pipelines.

## 14. How Docker components communicate 
The components work together through a sequence of operations.

```text
USER
 |
 v
Docker CLI
 |
 v
Docker API
 |
 v
Docker Daemon
 |
 v
containerd
 |
 v
containerd-shim
 |
 v
runc
 |
 v
Running Container
```

The docker daemon also coordinates image management, network configuration, volume mounting and registry operations. 

The components do not all communicate directly with one another. Each has a defined responsibility. 

