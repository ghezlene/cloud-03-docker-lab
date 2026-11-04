# Docker - Architecture & Technical Documentation

> A structured learning and practical documentation repository covering Docker architecture, components , containerization, networking, storage, orchestration and security. 

## 1. Overview 

Docker is a containerization platform that allows applications and their dependencies to be packaged into isolated, portable and reproducible environments called containers. 

Instead of installing and configuring applications directly on every machine, Docker allows developers and system administrators to build application images and run them as containers. 

### Chy Docker? 
- **Portability:** Run applications consistently across different environments. 
- **Isolation:** Separate application processes and dependencies. 
- **Reproducibility:** Use the same image to recreate an environment.
- **Scalability:** Run multiple instances of an application. 
- **Automation:** Integrate containerization into CI/CD pipelines. 
- **Resource efficiency:** Share the host operating system kernel instead of running a complete guest OS for every container. 

---

## 2. Docker Architecture

Docker follows a client-server architecture. 

The docker client communicates with the docker doemon through Docker API. The daemon manages Docker objects such as images, containers, networks and volumes. 

### Architecture Diagram 

```text 
     USER
                   |
                   v
             Docker CLI
             (docker)
                   |
                   | Docker API
                   v
        +------------------------+
        |     Docker Daemon      |
        |       (dockerd)        |
        +------------------------+
                   |
          +--------+--------+
          |        |        |
          v        v        v
       Images   Containers Networks
          |        |        |
          +--------+--------+
                   |
                   v
                Volumes

          Docker Registry
          (Docker Hub, etc.)
                   ^
                   |
             Pull / Push
```

##Main architectural Components 

| Compoonent | Responsability | 
|---|---|
| Docker client | Sends Commands to the docker daemon | 
| Docker daemon | Manages Docker objects and operations | 
| Docker API | Provides communication between clients and the daemon | 
| Docker images | Readonly templates used to create containers | 
| Docker containers | Running or stopped instances created from images | 
| Docker networks | Enable communication between containers and external systems | 
| Docker volumes | Provide persistent storage for container data |
| Docker Registry | stores and distributes Docker images | 
| containerd | manages container lifecycle operations | 
| runc | Creates and runs containers according to OCI specificaations |


