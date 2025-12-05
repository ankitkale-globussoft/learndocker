# Docker Tutorial

## Introduction to Docker

Docker is a platform that facilitates the building of containers.

A **container** consists of an application and its dependencies.

### Properties of Containers

- **Portable**: Can be shared across different machines.
- **Lightweight**: Minimal resource overhead.
- **Isolated Environments**: Allows creation of separate environments on the same machine using containers.

## Docker Images

A Docker image is an executable file containing commands to build a Docker container. It serves as a blueprint for creating containers (similar to the relationship between a class and an object in programming).

Docker images are shared, and containers are created from these images.

## Docker Commands

1. `docker pull IMAGE_NAME`  
   Pulls the image if it does not exist on the local machine.

2. `docker images`  
   Displays all images available on the system.

3. `docker run IMAGE_NAME`  
   Builds and runs a container using the specified image.

4. `docker run -it IMAGE_NAME`  
   Runs the container in interactive mode, providing access to its terminal.

5. `docker ps -a`  
   Shows all containers (running and stopped).

6. `docker ps`  
   Shows only active (running) containers.

7. `docker start CONT_NAME or CONT_ID`  
   Restarts an existing container.

8. `docker stop CONT_NAME or CONT_ID`  
   Stops a running container.

9. `docker rm CONT_ID/NAME`  
   Removes a container.

10. `docker rmi IMG_ID/NAME`  
    Removes a Docker image.

11. `docker pull IMAGE_NAME:VERSION`  
    Pulls a specific version of an image.

12. `docker run -d IMAGE_NAME`  
    Runs the container in detached mode (background).

13. `docker run --name CONT_NAME -d IMAGE_NAME`  
    Runs the container in detached mode with a custom name.

## Docker Image Layers

Docker images are composed of layers:

```
Container (mutable layer)
|
Layer 2 (immutable)
|
Layer 1 (immutable)
|
Base Layer (small-sized Linux-based image, immutable)
```

All layers are immutable except the container layer, which is mutable.

## Port Binding

By default, containers operate with their own isolated ports, separate from the host machine's ports.

To bind a host machine port to a container port, use port binding.

```bash
docker run -p HOST_PORT:CONT_PORT IMAGE_NAME
```

**Example**:
```bash
docker run -p 8080:3306 IMAGE_NAME
```

### Troubleshooting Commands

14. `docker logs CONT_ID`  
    Displays the logs of a container.

15. `docker exec -it CONT_ID /bin/bash` or `docker exec -it CONT_ID /bin/sh`  
    Provides access to the container's shell to inspect files, environment, etc.

## Docker vs. Virtual Machine

| Aspect              | Docker                                      | Virtual Machine (VM)                          |
|---------------------|---------------------------------------------|-----------------------------------------------|
| **Virtualization Level** | Uses the host machine's kernel; virtualizes only the application layer. | Uses its own kernel; virtualizes both the host OS and application layer. |
| **Layers**          | Application Layer<br>Host OS Kernel<br>Hardware | Application Layer<br>Guest OS<br>Hypervisor<br>Host OS Kernel<br>Hardware |
| **Performance**     | Lightweight due to shared kernel.           | Heavier due to full OS emulation.             |

**Disadvantages**: Docker was initially designed for Linux-based systems. Docker Desktop introduces a lightweight hypervisor layer that utilizes a minimal Linux distribution internally. In essence, Docker Desktop runs containers within a small Linux-based VM.

## Developing with Docker

### Docker Networks

Docker networks enable internal connections between containers on the same network.

16. `docker network ls`  
    Lists all Docker networks on the system.

17. `docker network create NetworkName`  
    Creates a new Docker network.

#### Example Practice Project (Node.js + HTML/CSS + MongoDB)

**Setup MongoDB**:
```bash
docker run -d \
  -p 27017:27017 \
  --name mongo \
  --network mongo-network \
  -e MONGO_INITDB_ROOT_USERNAME=admin \
  -e MONGO_INITDB_ROOT_PASSWORD=qwerty \
  mongo
```

**Setup Mongo-Express**:
```bash
docker run -d \
  -p 8081:8081 \
  --name mongo-express \
  --network mongo-network \
  -e ME_CONFIG_MONGODB_AUTH_USERNAME=admin \
  -e ME_CONFIG_MONGODB_AUTH_PASSWORD=qwerty \
  -e ME_CONFIG_MONGODB_URL="mongodb://admin:qwerty@mongo:27017" \
  mongo-express
```

Default credentials for Mongo-Express: Username - `admin`, Password - `qwerty`.

### Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications. It uses a YAML (Yet Another Markup Language) file to specify configurations.

The YAML file includes instructions for required containers, such as:

```
version: ""  # Optional

services:
  service_name:
    image: IMAGE_NAME
    ports:
      - "27017:27017"
    environment:
      - MONGO_INITDB_ROOT_PASSWORD=...
```

#### Docker Compose Commands

18. `docker compose -f fileName.yaml up -d`  
    Creates and runs containers defined in the YAML file in detached mode.

19. `docker compose -f fileName.yaml down`  
    Permanently deletes/removes the containers defined in the YAML file.

**Note**: For containers defined in the YAML file, Docker Compose automatically creates a dedicated network and runs the containers within it, eliminating the need for manual network creation.

**Important**: Docker does not provide data persistence by default. Restarting Docker or a container will result in loss of changes.

## Dockerizing an Application

The process for dockerizing an app (e.g., TestApp) is:

```
TestApp
|
Docker Image
|
Container
```

Jenkins can be used to automate the conversion of the application into a Docker image. A `Dockerfile` contains the instructions for building the image.

### Important Dockerfile Instructions

- **FROM base_image**: Specifies the base image (e.g., Node.js for a Node.js app) with required dependencies.
- **WORKDIR path**: Sets the working directory in the image where files are copied and commands are executed.
- **COPY host_path image_path**: Copies files from the host machine to the image.
- **RUN command**: Executes a command during the build process (multiple RUN commands are allowed).
- **CMD command**: Specifies the command to execute when the container starts (only one CMD is allowed).
- **EXPOSE port**: Exposes a port from the image.
- **ENV key=value**: Defines environment variables.

After creating the `Dockerfile`, build the image with:

20. `docker build -t APP_NAME:VERSION .`