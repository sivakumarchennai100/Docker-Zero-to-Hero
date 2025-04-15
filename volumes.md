# Docker Volumes

## Problem Statement

It is a very common requirement to persist the data in a Docker container beyond the lifetime of the container. However, the file system
of a Docker container is deleted/removed when the container dies. 

Lets say we have installed an nginx application inside the container, it continously puts the user information inside the log file like who is the logged in user and from which ip add, etc., 

Since container is ephimeral in nature, the logfiles get deleted when the container is down.

---------------------------------------------------------

Examples:

Scenario1:
Container --> NGINX installed --> Storage of log files
Container down --> NGINX down --> Log files deleted.

Scenario2: Frontend Container --> Fetching data from files stored by the backend container <-- Backend Container (Down)

Scenario3: App container --> Fetching the file that is provided from the cron job on the host >> Container cannot access the file from the host directories, it can only access the resources.

For the above scenarios , docker offers 2 solutions

1. Bind mounts >> Allow to bind a directory on the host with the folder inside a container
2. Volumes >> Same as Bind mounts with better lifecycles
              Using Docker cli >> create/remove/move volumes >>
              Binding the folder bw Container and any hosts can be done with volumes
              High performance
---------------------------------------------------------

## Solution

There are 2 different ways how docker solves this problem.

1. Volumes
2. Bind Directory on a host as a Mount

### Volumes 

Volumes aims to solve the same problem by providing a way to store data on the host file system, separate from the container's file system, 
so that the data can persist even if the container is deleted and recreated.

![image](https://user-images.githubusercontent.com/43399466/218018334-286d8949-d155-4d55-80bc-24827b02f9b1.png)


Volumes can be created and managed using the docker volume command. You can create a new volume using the following command:

```
docker volume create <volume_name>
```

Once a volume is created, you can mount it to a container using the -v or --mount option when running a docker run command. 

For example:

```
docker run -it -v <volume_name>:/data <image_name> /bin/bash
```

This command will mount the volume <volume_name> to the /data directory in the container. Any data written to the /data directory
inside the container will be persisted in the volume on the host file system.

### Bind Directory on a host as a Mount

Bind mounts also aims to solve the same problem but in a complete different way.

Using this way, user can mount a directory from the host file system into a container. Bind mounts have the same behavior as volumes, but
are specified using a host path instead of a volume name. 

For example, 

```
docker run -it -v <host_path>:<container_path> <image_name> /bin/bash
```

## Key Differences between Volumes and Bind Directory on a host as a Mount

Volumes are managed, created, mounted and deleted using the Docker API. However, Volumes are more flexible than bind mounts, as 
they can be managed and backed up separately from the host file system, and can be moved between containers and hosts.

In a nutshell, Bind Directory on a host as a Mount are appropriate for simple use cases where you need to mount a directory from the host file system into
a container, while volumes are better suited for more complex use cases where you need more control over the data being persisted
in the container.

---------------------------------------------------------

Practicals:

[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$ docker volume create siva
siva
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$ docker volume ls
DRIVER    VOLUME NAME
local     siva
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$ docker volume inspect siva
[
    {
        "CreatedAt": "2024-09-04T05:58:31Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/siva/_data",
        "Name": "siva",
        "Options": null,
        "Scope": "local"
    }
]
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$


Delete:
-----
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$ docker volume rm siva
siva
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$ docker volume ls
DRIVER    VOLUME NAME
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$



Creating a container and mounting the volumes:
---------------------------------------------

[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$ docker images | head -5
REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
simplecalculator-multistage         latest    7432c55f3f1c   24 minutes ago   1.96MB
simplecalculator                    latest    2dfe4e10dee9   30 minutes ago   666MB
skmohan7984/my-first-docker-image   latest    43a98e3721dc   5 hours ago      580MB
hello-world                         latest    d2c94e258dcb   16 months ago    13.3kB
[ec2-user@ip-172-31-83-217 golang-multi-stage-docker-build]$


Note: If Entry point is there in the Docker file, will be difficult to understand

[ec2-user@ip-172-31-83-217 ~]$ vim Dockerfile

FROM ubuntu && wq!


Using the git hub repo docker file for creating the docker container:

[ec2-user@ip-172-31-83-217 first-docker-file]$ pwd
/home/ec2-user/Docker-Zero-to-Hero/examples/first-docker-file


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker build -t volumedemo .


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker images | head -5
REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
simplecalculator-multistage         latest    7432c55f3f1c   30 minutes ago   1.96MB
simplecalculator                    latest    2dfe4e10dee9   36 minutes ago   666MB
skmohan7984/my-first-docker-image   latest    43a98e3721dc   5 hours ago      580MB
volumedemo                          latest    43a98e3721dc   5 hours ago      580MB
[ec2-user@ip-172-31-83-217 first-docker-file]$

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker volume ls
DRIVER    VOLUME NAME
local     siva
local     siva1


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker run -d --mount source=siva,target=/app nginx:latest      <<<<<<<<<<<<<
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
e4fff0779e6d: Pull complete
2a0cb278fd9f: Pull complete
7045d6c32ae2: Pull complete
03de31afb035: Pull complete
0f17be8dcff2: Pull complete
14b7e5e8f394: Pull complete
23fa5a7b99a6: Pull complete
Digest: sha256:447a8665cc1dab95b1ca778e162215839ccbb9189104c79d7ec3a81e14577add
Status: Downloaded newer image for nginx:latest
a6f73cfb6b1116766a836b3f52748c697dd7212dbcb94eab65471a3b2679005a
[ec2-user@ip-172-31-83-217 first-docker-file]$



[ec2-user@ip-172-31-83-217 first-docker-file]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
a6f73cfb6b11   nginx:latest   "/docker-entrypoint.…"   58 seconds ago   Up 56 seconds   80/tcp    trusting_cohen
[ec2-user@ip-172-31-83-217 first-docker-file]$



[ec2-user@ip-172-31-83-217 first-docker-file]$ docker inspect a6f73cfb6b11
>> Gives the full details of the container

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker volume rm siva
Error response from daemon: remove siva: volume is in use - [a6f73cfb6b1116766a836b3f52748c697dd7212dbcb94eab65471a3b2679005a]
[ec2-user@ip-172-31-83-217 first-docker-file]$

Note: Inorder to delete the volumes, we need to first stop the container and delete the container , then only we can delete the volumes
$ docker ps -a --filter "volume=siva"
$ docker rm 3bce43fba923
$ docker volume rm siva
***********************************************************************************




sudo du -ah . | sort -rh | head -20
sudo lsof | grep '(deleted)'

https://kaiwern.com/posts/2020/03/16/aws-ec2-disk-space-full/


--------------------------------------------------------------------------------
