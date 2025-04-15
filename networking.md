# Docker Networking

Networking allows containers to communicate with each other and with the host system. Containers run isolated from the host system
and need a way to communicate with each other and with the host system.

By default, Docker provides two network drivers for you, the bridge and the overlay drivers. 

```
docker network ls
```

```
NETWORK ID          NAME                DRIVER
xxxxxxxxxxxx        none                null
xxxxxxxxxxxx        host                host
xxxxxxxxxxxx        bridge              bridge
```


### Bridge Networking

The default network mode in Docker. It creates a private network between the host and containers, allowing
containers to communicate with each other and with the host system.

![image](https://user-images.githubusercontent.com/43399466/217745543-f40e5614-ac34-4b78-85a9-91b24512388d.png)

If you want to secure your containers and isolate them from the default bridge network you can also create your own bridge network.

```
docker network create -d bridge my_bridge
```

Now, if you list the docker networks, you will see a new network.

```
docker network ls

NETWORK ID          NAME                DRIVER
xxxxxxxxxxxx        bridge              bridge
xxxxxxxxxxxx        my_bridge           bridge
xxxxxxxxxxxx        none                null
xxxxxxxxxxxx        host                host
```

This new network can be attached to the containers, when you run these containers.

```
docker run -d --net=my_bridge --name db training/postgres
```

This way, you can run multiple containers on a single host platform where one container is attached to the default network and 
the other is attached to the my_bridge network.

These containers are completely isolated with their private networks and cannot talk to each other.

![image](https://user-images.githubusercontent.com/43399466/217748680-8beefd0a-8181-4752-a098-a905ebed5d2a.png)


However, you can at any point of time, attach the first container to my_bridge network and enable communication

```
docker network connect my_bridge web
```

![image](https://user-images.githubusercontent.com/43399466/217748726-7bb347d0-3736-4f89-bdff-31d240b15150.png)


### Host Networking

This mode allows containers to share the host system's network stack, providing direct access to the host system's network.

To attach a host network to a Docker container, you can use the --network="host" option when running a docker run command. When you use this option, the container has access to the host's network stack, and shares the host's network namespace. This means that the container will use the same IP address and network configuration as the host.

Here's an example of how to run a Docker container with the host network:

```
docker run --network="host" <image_name> <command>
```

Keep in mind that when you use the host network, the container is less isolated from the host system, and has access to all of the host's network resources. This can be a security risk, so use the host network with caution.

Additionally, not all Docker image and command combinations are compatible with the host network, so it's important to check the image documentation or run the image with the --network="bridge" option (the default network mode) first to see if there are any compatibility issues.

### Overlay Networking

This mode enables communication between containers across multiple Docker host machines, allowing containers to be connected to a single network even when they are running on different hosts.

### Macvlan Networking

This mode allows a container to appear on the network as a physical host rather than as a container.



*********************************************************************************************8
### My Notes:

Day 28: https://www.youtube.com/watch?v=xrUGEoUpa3s

Scenario: Host/EC2 --> Docker --> Containers

In the above we need 1. one container should talk to another container eg., frontend appln should talk to backend appln.
                  Host (etho: 192.16.3.4) + Container1 (etho: 172.16.1.1), when we try to ping the Container eth0 to Host eth0, it will not ping.
                        To solve the above problem, whenever a container is created, docker creates a "v-eth" interface which is basically docker0, without this virtual network a container cannot tak to the host.
                  By default whenever a container is created virtual interface or docker0 gets created, this is called a bridge networking.
                  
                  - The other network we have is host networking, means whenever the container is created docker will directly use the network of the host ie., bind the eth0 of the host to the eth0 of the container
                    
                    Host networking is the insecure way of networking because both the cntainer and the hosts are in the same networks
                  
                  - Overlay network: Whenever there are multiple hosts, then overlay network creates a network among the different hosts.
                    Overlay networking can be discussed when we go through the kubernetes and docker swarm.             

                     2. One container should not talk to another container eg., login container should nt talk to payroll container. 
                  All th containers created can talk within eachother and to host throuh the veth/docker0 which is called OOTB (Out of the Box) which is not secure
                  If one container dont want to communicate to other container, then a custom bridge nw is created between the container and the host inorder not to communicate to the other containers.
                  The above scenario is useful when one conatiner wants to communicate to another container
                  Now when one container dont want to communicate with other container then it can be achieved by Custom Bridge networking.Since in host networking all the containers shares the same network of the hosts.



[ec2-user@ip-172-31-83-217 first-docker-file]$ docker run -d --name login  nginx:latest >>> Creating a container in detached mode.
40d050440f96285e502e60ba45a1003e42b212fc88526337fab58507589e492f
[ec2-user@ip-172-31-83-217 first-docker-file]$


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker exec -it login /bin/bash  >> login to the created container
root@40d050440f96:/#

root@40d050440f96:/# apt update
Get:1 http://deb.debian.org/debian bookworm InRelease [151 kB]
Get:2 http://deb.debian.org/debian bookworm-updates InRelease [55.4 kB]
Get:3 http://deb.debian.org/debian-security bookworm-security InRelease [48.0 kB]
Get:4 http://deb.debian.org/debian bookworm/main amd64 Packages [8787 kB]
Get:5 http://deb.debian.org/debian bookworm-updates/main amd64 Packages [13.8 kB]
Get:6 http://deb.debian.org/debian-security bookworm-security/main amd64 Packages [179 kB]
Fetched 9234 kB in 1s (8023 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
10 packages can be upgraded. Run 'apt list --upgradable' to see them.
root@40d050440f96:/#


Install PING :

root@40d050440f96:/# apt-get install iputils-ping -y

root@40d050440f96:/# ping -V
ping from iputils 20221126
libcap: yes, IDN: yes, NLS: no, error.h: yes, getrandom(): yes, __fpending(): yes
root@40d050440f96:/#



[ec2-user@ip-172-31-83-217 first-docker-file]$ docker run -d --name logout  nginx:latest
b2e5d58d188b4758d6aef463db5613091467fc9b383209fd8d0c92e127fc15ac
[ec2-user@ip-172-31-83-217 first-docker-file]$
[ec2-user@ip-172-31-83-217 first-docker-file]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
b2e5d58d188b   nginx:latest   "/docker-entrypoint.…"   15 seconds ago   Up 14 seconds   80/tcp    logout "IPAddress": "172.17.0.4"
40d050440f96   nginx:latest   "/docker-entrypoint.…"   5 minutes ago    Up 5 minutes    80/tcp    login "IPAddress": "172.17.0.3"
a6f73cfb6b11   nginx:latest   "/docker-entrypoint.…"   46 minutes ago   Up 46 minutes   80/tcp    trusting_cohen
[ec2-user@ip-172-31-83-217 first-docker-file]$
[ec2-user@ip-172-31-83-217 first-docker-file]$   >>> we can get the ip address of the container


Trying to ping logout container from login container:

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
b2e5d58d188b   nginx:latest   "/docker-entrypoint.…"   3 minutes ago    Up 3 minutes    80/tcp    logout
40d050440f96   nginx:latest   "/docker-entrypoint.…"   8 minutes ago    Up 8 minutes    80/tcp    login
a6f73cfb6b11   nginx:latest   "/docker-entrypoint.…"   49 minutes ago   Up 49 minutes   80/tcp    trusting_cohen
[ec2-user@ip-172-31-83-217 first-docker-file]$
[ec2-user@ip-172-31-83-217 first-docker-file]$
[ec2-user@ip-172-31-83-217 first-docker-file]$
[ec2-user@ip-172-31-83-217 first-docker-file]$ docket exec -it 40d050440f96 /bin/bash
-bash: docket: command not found
[ec2-user@ip-172-31-83-217 first-docker-file]$ docker exec -it 40d050440f96 /bin/bash
root@40d050440f96:/#
root@40d050440f96:/# ping 172.17.0.4
PING 172.17.0.4 (172.17.0.4) 56(84) bytes of data.
64 bytes from 172.17.0.4: icmp_seq=1 ttl=127 time=0.070 ms
64 bytes from 172.17.0.4: icmp_seq=2 ttl=127 time=0.061 ms
64 bytes from 172.17.0.4: icmp_seq=3 ttl=127 time=0.055 ms
^C
--- 172.17.0.4 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2089ms
rtt min/avg/max/mdev = 0.055/0.062/0.070/0.006 ms
root@40d050440f96:/#


To list all the network on the host:

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
838dd6228648   bridge    bridge    local
ca515ee09846   host      host      local
3f6cf8172960   none      null      local
[ec2-user@ip-172-31-83-217 first-docker-file]$


To create and delete the network :

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker network create test
4da438b54df8d6da289265650e280263d8d2c11670688c4b7de7c7eaecd65287
[ec2-user@ip-172-31-83-217 first-docker-file]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
838dd6228648   bridge    bridge    local
ca515ee09846   host      host      local
3f6cf8172960   none      null      local
4da438b54df8   test      bridge    local
[ec2-user@ip-172-31-83-217 first-docker-file]$ docker network rm test
test
[ec2-user@ip-172-31-83-217 first-docker-file]$
[ec2-user@ip-172-31-83-217 first-docker-file]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
838dd6228648   bridge    bridge    local
ca515ee09846   host      host      local
3f6cf8172960   none      null      local
[ec2-user@ip-172-31-83-217 first-docker-file]$



-------------------------------------------------


To create a finance container and assgin a secure network:


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker run -d --name finance --network=secure-network nginx:latest
55d0b0136c3029f9c943f6677e889802d569d053d19578ce41ad568a4b649e30
[ec2-user@ip-172-31-83-217 first-docker-file]$


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker ps -a
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
55d0b0136c30   nginx:latest   "/docker-entrypoint.…"   18 seconds ago   Up 18 seconds   80/tcp    finance "IPAddress": "172.19.0.2"
b2e5d58d188b   nginx:latest   "/docker-entrypoint.…"   10 minutes ago   Up 10 minutes   80/tcp    logout "IPAddress": "172.17.0.4"
40d050440f96   nginx:latest   "/docker-entrypoint.…"   16 minutes ago   Up 16 minutes   80/tcp    login "IPAddress": "172.17.0.3"
a6f73cfb6b11   nginx:latest   "/docker-entrypoint.…"   57 minutes ago   Up 57 minutes   80/tcp    trusting_cohen


[ec2-user@ip-172-31-83-217 first-docker-file]$ docker inspect finance
          "Networks": {
                "secure-network": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "55d0b0136c30"
                    ],
                    "MacAddress": "02:42:ac:13:00:02",
                    "NetworkID": "767bb579f7faef0f0f8d2a725219308ad82df56626bc20c03b7cd69fca13e80e",
                    "EndpointID": "ba67b9f9de58c65d46793dd5fc183a3b3c0fdb8d48d84be0fde3b10dc76b2d4f",
                    "Gateway": "172.19.0.1",
                    "IPAddress": "172.19.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,




Now when we try to ping the finance container from login container, will not ping, since we have made isolation via secure-network.
[ec2-user@ip-172-31-83-217 first-docker-file]$ docker exec -it 40d050440f96 /bin/bash
root@40d050440f96:/# ping 172.19.0.2
PING 172.19.0.2 (172.19.0.2) 56(84) bytes of data.
^C
--- 172.19.0.2 ping statistics ---
90 packets transmitted, 0 received, 100% packet loss, time 92571ms



---------------------------------------------------------------

Creating a host network and check:

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker run -d --name hostcont --network=host nginx:latest
fca63172fab62e9fe5af63ccdc7dc151e8cff7fd47e9b5ec374de2fe212a87aa
[ec2-user@ip-172-31-83-217 first-docker-file]$

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS     NAMES
fca63172fab6   nginx:latest   "/docker-entrypoint.…"   About a minute ago   Up About a minute             hostcont
55d0b0136c30   nginx:latest   "/docker-entrypoint.…"   6 minutes ago        Up 6 minutes        80/tcp    finance
b2e5d58d188b   nginx:latest   "/docker-entrypoint.…"   17 minutes ago       Up 17 minutes       80/tcp    logout
40d050440f96   nginx:latest   "/docker-entrypoint.…"   22 minutes ago       Up 22 minutes       80/tcp    login
a6f73cfb6b11   nginx:latest   "/docker-entrypoint.…"   About an hour ago    Up About an hour    80/tcp    trusting_cohen
[ec2-user@ip-172-31-83-217 first-docker-file]$

[ec2-user@ip-172-31-83-217 first-docker-file]$ docker inspect hostcont
           "Networks": {
                "host": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "",
                    "NetworkID": "ca515ee098463faf8bafca0fabddae7dc237a69e1d560261b92eff0f10d89c1f",
                    "EndpointID": "e5665bcd1fba9a72f9698ca430000560e8dcadd5b8ef68112223ee25167fd891",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DriverOpts": null,
                    "DNSNames": null

> No ip address b/c we can directly access the container from the host itself


***********************************************************************************************************************



