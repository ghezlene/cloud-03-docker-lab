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
