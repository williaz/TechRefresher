
- Docker is a standard for Linux containers.
  - A “Container” is an isolated runtime inside of Linux.
  - A “Container” provides a private machine like space under Linux. 
  - Containers will run under any modern Linux Kernel
- Containers can:
  - Have their own process space.
  - Their own network interface.
  - ‘Run’ processes as root (inside the container)
  - Have their own disk space. (can share with host too)
- Docker Image - The representation of a Docker Container. Kind of like a JAR or WAR file in Java.
- Docker Container - The standard runtime of Docker. Effectively a deployed and running Docker Image. 
Like a Spring Boot Executable JAR.
- Docker Engine - The code which manages Docker stuff. Creates and runs Docker Containers.
- Enterprise edition: CaaS

```ash
docker ps
docker images -a
docker run hello-world
```
- Kitematic
  - automates the Docker installation and setup process
  - provides an intuitive graphical user interface (GUI) for running Docker containers

- Mongo
  - default port: 27017
```bash
$ docker run -p 27017:27017 -d mongo
cfb54c2e7ff7c5cb4b112b217ed4c782d30dd6feb70c3ceb0530fe169e4ed5a0
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
cfb54c2e7ff7        mongo               "docker-entrypoint.s…"   6 seconds ago       Up 5 seconds        0.0.0.0:27071->27017/tcp   admiring_meninsky

$ docker logs -f cfb54c2e7ff

$ docker run -p 27017:27017 -v /Users/williaz/dockerdata/mongo:/data/db -d mongo
```
- image
  - An Image defines a Docker Container.
  - Immutable, built in layers
  - mount folder for persistent data in case lost when container restart
