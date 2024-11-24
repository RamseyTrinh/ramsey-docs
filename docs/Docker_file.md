# Dockerfile

## Dockerfile path

- FROM - The os used. Common is alpine, debian, ubuntu
- ENV - Environment variables
- RUN - Run commands/shell scripts, etc
- EXPOSE - Ports to expose
- CMD - Final command run when you launch a new container from image
- WORKDIR - Sets working directory (also could use 'RUN cd /some/path')
- COPY # Copies files from host to container

## Build with Dockerfile

### From the same directory as Dockerfile

```
$ docker image build -t [REPONAME] .
```
### Benchmarking builds

```
$ DOCKER_BUILDKIT=1 docker image build -t [REPONAME] .
```

### Tip: Cache and Order

- If you re-run the build, it will be quick because everythging is cached.
- If you change one line and re-run, that line and everything after will not be cached
- Keep things that change the most toward the bottom of the Dockerfile

## Usual step

Build image from Dockerfile

```
$ docker image build -t nginx
```
Running it

```
$ docker container run -p 80:80 --rm nginx
```
Tag 

```
$ docker image tag nginx:latest ramseytrinh/nginx:latest
```
Push to Dockerhub
```
$ docker image push ramseytrinh/nginx:latest
```

## DOCKER COMPOSE

- Configure relationships between containers
- Save our docker container run settings in easy to read file

### Sample compose file

```
version: '2'

# same as
# docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'
```


### Example 2
```
version: '3'
services:
  app:
    container_name: docker-node-mongo
    restart: always
    build: .
    ports:
      - '80:3000'
    links:
      - mongo
  mongo:
    container_name: mongo
    image: mongo
    ports:
      - '27017:27017'
```

### To run

```
docker-compose up
```

### You can run in background with

```
docker-compose up -d
```

### To cleanup

```
docker-compose down
```
