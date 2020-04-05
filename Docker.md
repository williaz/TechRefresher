
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
