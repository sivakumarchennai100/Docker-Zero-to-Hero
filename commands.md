# Docker Commands

Some of the most commonly used docker commands are 

### docker images

Lists docker images on the host machine.

### docker build

Builds image from Dockerfile.

### docker run

Runs a Docker container. 

There are many arguments which you can pass to this command for example,

`docker run -d` -> Run container in background and print container ID
`docker run -p` -> Port mapping

use `docker run --help` to look into more arguments.

### docker ps

Lists running containers on the host machine.

### docker stop

Stops running container.

### docker start

Starts a stopped container.

### docker rm

Removes a stopped container.

### docker rmi

Removes an image from the host machine.


### docker push

Uploads an image to the configured registry.

### docker exec

Run a command in a running container.

### docker network

Manage Docker networks such as creating and removing networks, and connecting containers to networks.

### Docker command cheat sheet:

Here’s a concise cheat sheet of essential Docker commands to help you manage Docker containers, images, networks, and volumes efficiently:

## Basic Docker Commands:

## Build an Image
docker build -t <image_name> .

## List Images
docker images

## Remove an Image
docker rmi <image_name>

## Pull an Image from Docker Hub
docker pull <image_name> --> Downloads an image from the configured registry.

## Run a Container
docker run -d -p <host_port>:<container_port> --name <container_name> <image_name>

## List Running Containers
docker ps

## List All Containers (including stopped)
docker ps -a

## Stop a Running Container
docker stop <container_name>

## Remove a Stopped Container
docker rm <container_name>

## Fetch and Follow Container Logs
docker logs -f <container_name>

## Open a Shell Inside a Running Container
docker exec -it <container_name> sh

#### Image Management Commands
## Tag an Image
docker tag <existing_image> <new_image:tag>

## Push an Image to Docker Hub
docker push <username>/<image_name>

#### Network and Volume Management
## List Networks
docker network ls

## Create a Network
docker network create <network_name>

## List Volumes
docker volume ls

## Create a Volume
docker volume create <volume_name>


## Advanced docker commands

Here are some advanced Docker commands that can help you manage and optimize your containerized applications more effectively:

#### Image Management
## Build an Image with No Cache
docker build -t <image_name> . --no-cache

## Prune Unused Images
docker image prune

## Inspect an Image
docker inspect <image_name>

#### Container Management
## Run a Container with Resource Limits
docker run -d --name <container_name> --memory="512m" --cpus="1" <image_name>

## Commit Changes to a Container
docker commit <container_id> <new_image_name>

## Export a Container to a Tar File
docker export <container_id> -o <file_name>.tar

## Import a Container from a Tar File
docker import <file_name>.tar

#### Network Management
## Create a Network with Specific Driver
docker network create --driver bridge <network_name>

## Connect a Container to a Network
docker network connect <network_name> <container_name>

## Disconnect a Container from a Network
docker network disconnect <network_name> <container_name>

#### Volume Management
## Create a Volume with Specific Options
docker volume create --name <volume_name> --opt type=tmpfs --opt device=tmpfs

## Inspect a Volume
docker volume inspect <volume_name>

## Remove Unused Volumes
docker volume prune

#### Advanced Container Operations
## Run a Container with Environment Variables

## Attach to a Running Container
docker attach <container_name>

## Monitor Resource Usage of Containers
docker stats

These commands can help you perform more complex tasks and optimize your Docker environment. 

#### Advanced Docker Commands
#### Image Management
## Build an Image with a Specific Dockerfile
docker build -f <Dockerfile_path> -t <image_name> .

## Save an Image to a Tar Archive
docker save -o <image_name>.tar <image_name>

## Load an Image from a Tar Archive
docker load -i <image_name>.tar

#### Container Management
## Run a Container with a Specific Restart Policy
docker run -d --name <container_name> --restart unless-stopped <image_name>

## Update Configuration of a Running Container
docker update --cpus 2 --memory 1G <container_name>

## Rename a Container
docker rename <old_container_name> <new_container_name>

#### Network Management
## Inspect a Network
docker network inspect <network_name>

## Remove a Network
docker network rm <network_name>

#### Volume Management
## Remove a Volume
docker volume rm <volume_name>

## Backup a Volume
docker run --rm -v <volume_name>:/volume -v $(pwd):/backup busybox tar cvf /backup/backup.tar /volume

## Restore a Volume
docker run --rm -v <volume_name>:/volume -v $(pwd):/backup busybox tar xvf /backup/backup.tar -C /volume

#### Advanced Container Operations
## Run a Container with a Custom Network
docker run -d --name <container_name> --network <network_name> <image_name>

## Run a Container with a Volume
docker run -d --name <container_name> -v <host_path>:<container_path> <image_name>

## Run a Container with a Specific User
docker run -d --name <container_name> --user <user_id>:<group_id> <image_name>

These commands can help you perform more complex tasks and optimize your Docker environment

************************************************************************************************************************


'
