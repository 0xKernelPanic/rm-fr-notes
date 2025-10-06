---
title: Docker
tags: 
created: 2025-02-19
---
 
# Docker

## Description
Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application.


### How does it work?

### Layers
When we create a container image, everything in the image is treated as read-only, and there is a new layer ovelayed on top that is read/write.

### Dockerfile

#### Example docker build
#####  Default example
```dockerfile
# Specify version and make less layers (if possible)
FROM ubuntu:latest
# FROM node:19.6-alpine

# Default directory
WORKDIR /usr/src/app

# make a environment variable
ENV NODE_ENV production

RUN apt update && apt install nodejs npm -y

# specify the user for security reasons
USER node

# Copy only src directory to the image and made it owned by the node user
COPY --chown=node:node ./src .

# will run on port 3000
EXPOSE 3000

RUN npm install

CMD ["npm","run","dev"]
```

##### Multistages example
A multi-stage build  in Docker is a way to build small, efficient container images by using multiple stages in a single Dockerfile. It helps to keep the final image lightweight by only including what is needed for running the application.
###### How It Works:

1. First Stage (Builder Stage)
    - This stage contains everything needed for building the application (compilers, dependencies, etc.).
    - It produces the final compiled or built artifact.
2. Second Stage (Final Image)
    - This stage is much smaller and only contains the final artifact.
    - It does not include unnecessary build dependencies.

Example for go application:
```Dockerfile
# First stage: Build the application
FROM golang:1.20 AS builder
WORKDIR /app

 # Non root user for security reasons
RUN useradd -u 1001 nonroot

COPY . .
RUN go build -o myapp

# Second stage: Create a lightweight final image
FROM alpine:latest
WORKDIR /app

COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /app/myapp .

USER nonroot

EXPOSE 8080
CMD ["./myapp"]
```

#### Build and list docker images
```bash
# To build dockerfile
docker build -t name:version .

# To list available docker images
docker image ls
```

### Docker-compose
Used to make in easy way containers talk to each other.

Microservices Is one big application broken down into multiple micro services

```yaml
version: "3.8"

services:
    mongo:
        image: mongo
        container_name: mongodb
        ports:
            - 27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME=admin
            - MONGO_INITDB_ROOT_PASSWORD=password
    mongo-express:
        image: mongo-express
        container_name: mongo-express
        ports:
            - 8081:8081
        environment:
            - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
            - ME_CONFIG_MONGODB_ADMINPASSWORD=password
            - ME_CONFIG_MONGODB_SERVER=mongodb
        depends_on:
            - mongo  # Bez "condition"
```

#### Basic
```bash
docker-compose -f <filename.yaml> -d up

docker-compose -f <filename.yaml> -d down

docker-compose -f <filename.yaml> start

docker-compose -f <filename.yaml> stop
```
## Commands
#### Basic
```bash
docker run -it --rm my-ubuntu-image

# Run ubuntu and commands after in shell
docker run -it ubuntu sh -c "apt update && apt install -y iputils-ping && bash"
```

```bash
# Build image with addicional content
docker build --tag my-ubuntu-image -<<EOF
FROM ubuntu:22.04
RUN apt update && apt install iputils-ping -y
EOF
```

#### Volume mounts
We can use volumes and mounts to safely persist the data. Its worth to know that the **my-volume** is located in `/var/lib/docker/volumes` in docker area on linux machine.
```bash
# Create volume
docker volume create my-volume

# Run docker with volume block
docker run -it --rm --mount source=my-volume,destination=/my-data/ ubuntu:22.04
```

#### Bind mounts
Alternatively, we can mount a directory from the host system using a bind mount.
```bash
# create a container that mounts a directory from the host filesystem into the container
docker run -it --rm --mount type=bind,source="${PWD}"/my-data,destination=/my-data ubuntu:22.04

# Similar but shorter syntax using -v
docker run -it --rm -v ${PWD}/my-data:/my-data ubuntu:22.04
```

#### Network

## Security
### Image Security
- keep attack surface area as small as possible (chainguard.dev):
	- Use minimal base images (multi-stage builds are a key enabler)
	- Dont\`t install things you don\`t need (e.g Dev deps)
- Scan images for vulnerabilities (Snyk or Trivy)
- Keep sensitive info out of images (use secrets, environment variable and args to store credentials)
- Sign and verify images
- Use fixed image tags
	- Either pin major.minor (allows patch fixes to be integrated)
	- Pin specific image hash
### Runtime Security
- Docker daemon (dockerd)
	- Start with `--userns-remap` option (https://docs.docker.com/engine/security/userns-remap/)
- Individual containers:
	- Use read only filesystem if writes are not needed
	- `--cap-drop=all`, then `--cap-add` anything you need
	- Limit cpu and memory `--cpus="0.5"`  `--memory 1024m` to limit utilizing of DDOS
	- Use --security-opt
		- seccomp profiles (https://docs.docker/engine/security/seccomp/)
		- apparmor profiles (https://docs.docker.com/engine/security/apparmor/)

## Examples
### Databases
Databases are botoriously fickle to install and configure. The instructions are often complex and vary accross different versions and operating system. For development, where you might need to run multiple versions of a single database or create a fresh database for testing purposes running in a container can be a massive improvement.

A few key considerations when running databases in containers:
- Use volume(s) to persist data to avoid data loss.
- Use bind mount(s) for additional config. You can create these files on your host system and bind mount them in the correct location within the container to be read upon startup.
- Set environment variables. For example setting admin password.

#### Postgres
```bash
docker run -d --rm \
  -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=foobarbaz \
  -p 5432:5432 \
  postgres:15.1-alpine

# With custom postresql.conf file
docker run -d --rm \
  -v pgdata:/var/lib/postgresql/data \
  -v ${PWD}/postgres.conf:/etc/postgresql/postgresql.conf \
  -e POSTGRES_PASSWORD=foobarbaz \
  -p 5432:5432 \
  postgres:15.1-alpine -c 'config_file=/etc/postgresql/postgresql.conf'
```

#### MySQL
```bash
docker run -d --rm \
  -v mysqldata:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=foobarbaz \
  -p 3306:3306 \
  mysql:8.0.32

# With custom conf.d
docker run -d --rm \
  -v mysqldata:/var/lib/mysql \
  -v ${PWD}/conf.d:/etc/mysql/conf.d \
  -e MYSQL_ROOT_PASSWORD=foobarbaz \
  -p 3306:3306 \
  mysql:8.0.32
```

### Interactive Test Environments
#### Operating systems
```bash
# https://hub.docker.com/_/ubuntu
docker run -it --rm ubuntu:22.04

# https://hub.docker.com/_/debian
docker run -it --rm debian:bullseye-slim
```
####  Programming runtimes
```bash
# https://hub.docker.com/_/python
docker run -it --rm python:3.11.1
```

### CLI Utilities

#### Sed
```bash
docker run -i --rm busybox:1.36.0 sed 's/file./file!/g' <sample-data/test.txt
```
#### Base64
```bash
# Pipe input from previous command
echo "This string is just long enough to trigger a line break in GNU base64." | docker run -i --rm busybox:1.36.0 base64

# Read input from file
docker run -i --rm busybox:1.36.0 base64 </sample-data/test.txt
```

#### Amazon Web Services CLI
```bash
# https://hub.docker.com/r/amazon/aws-cli
# Bind mount the credentials into the container
docker run --rm -v ~/.aws:/root/.aws amazon/aws-cli:2.9.18 s3 ls
```

#### Localstack
```bash
docker run --rm -it -p 4566:4566 -p 4510-4559:4510-4559 localstack/localstack
```
#### Cyberchef
```bash
docker run -d -p 8000:8000 mpepping/cyberchef
```


## Resources
- https://www.youtube.com/watch?v=RqTEHSBrYFw&t=2886s