# Docker cheat sheet

## Docker version

```shell
$ docker version
```

## Show info

```shell
$ docker info
```
## Some flag in command

```bash
-d: run in "detached" mode
-i: keep STDIN open 
-t: allocate a terminal interating with container
-it: access terminal
--name: name
-p: map ports
--rm: remove the container when it exits.
-v: mout a volume
--network: specify the network (Default: bridge)
-e: environment variables
--link: link to another running container
--restart: set the restart policy
```

## Images commands

### List images

```
$ docker image ls
or
$ docker images
```

### Pull down images

```
$ docker pull [IMAGE]
```

### Remove image

```
$ docker image rm [IMAGE]
$ docker rmi [IMAGE]
```

### Remove all images

```
$ docker rmi $(docker images -a -q)
```

### Some sample container creation

NGINX:

```bash
$ docker container run -d -p 80:80 --name nginx nginx
```

APACHE:

```bash
$ docker container run -d -p 8080:80 --name apache httpd
```

MONGODB:

```bash
$ docker container run -d -p 27017:27017 --name mongo mongo
```

MYSQL:

```bash
$ docker container run -d -p 3306:3306 --name mysql --env MYSQL_ROOT_PASSWORD=123456 mysql
```
## Containers commands

### Create and run

```bash
$ docker container run -it -p 80:80 nginx
```

### List running

```
$ docker ps
```

### List all

```
$ docker ps -a
```

### Stop

```
$ docker container stop []
```

### Stop all running

```
$ docker stop $(docker ps -aq)
```

### Force remove

```
$ docker container rm -f [ID]
```

### Remove multiple

```
$ docker container rm [ID] [ID] [ID]
```

### Remove all

```
$ docker rm $(docker ps -aq)
```

### Get logs (Use name or ID)

```
$ docker container logs [NAME]
```

### TIP: About containers

Docker containers are often compared to virtual machines but they are actually just processes running on your host os. In Windows/Mac, Docker runs in a mini-VM so to see the processes youll need to connect directly to that. On Linux however you can run "ps aux" and see the processes directly

### View info

```
$ docker container inspect [NAME]
```

### Performance stats info

```
$ docker container stats [NAME]
```

### Access an already created

```
$ docker container start -ai ubuntu
```

### Edit config

```bash
$ docker container exec -it mysql bash
```

## Networking

"bridge" or "docker0" is the default network

### Get port

```
$ docker container port [NAME]
```

### List networks

```
$ docker network ls
```

### Inspect network

```
$ docker network inspect [NETWORK_NAME]
("bridge" is default)
```

### Create network

```
$ docker network create [NETWORK_NAME]
```

or 
```bash
$ docker network create --driver bridge [NETWORK_NAME]
```

### Connect existing container to network

```
$ docker network connect [NETWORK_NAME] [CONTAINER_NAME]
```

### Disconnect container from network

```
$ docker network disconnect [NETWORK_NAME] [CONTAINER_NAME]
```

### Detach network from container

```
$ docker network disconnect
```

### Delete/Remove network

To remove the network by name or id, multiple can be deleted:

```shell
$ docker network rm [NETWORK_NAME] [NETWORK_NAME]
```

## Image tagging & Push to Dockerhub

```
$ docker image ls
```

### Retag existing image

```
$ docker image tag nginx btraversy/nginx
```

### Upload to dockerhub

```
$ docker image push bradtraversy/nginx
```

## Volumes

Volume - Makes special location outside of container UFS. Used for databases
Bind Mount -Link container path to host path

### Check volumes

```
$ docker volume ls
```

### Cleanup unused volumes

```
$ docker volume prune
```



### TIP: Mounts

- You will also see the volume under mounts
- Container gets its own uniqe location on the host to store that data
- Source: xxx is where it lives on the host

### Check volumes

```
$ docker volume ls
```

**There is no way to tell volumes apart for instance with 2 mysql containers, so we used named volumes**

### Named volumes (Add -v command)

```
$ docker container run -d --name mysql -v mysql-db:/var/lib/mysql mysql
```

### Inspect new named volume

```bash
docker volume inspect mysql-db
```