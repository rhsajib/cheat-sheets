#### Install Docker
- https://github.com/docker/for-mac/issues/6671
- [Suitable latest version(4.15) of docker for mac os catalina 10.15.7](https://desktop.docker.com/mac/main/amd64/93002/Docker.dmg)
- [Docker Desktop release notes](https://docs.docker.com/desktop/release-notes/)

#### Commands for terminal
After installing docker desktop
```
Login to docker (if not logged in)
~ % docker login
```

```
If any error occurs after login, try logout first, then login again
~ % docker logout
~ % docker login
```

```
Create Dockerfile

see below
```
```
Build docker image
Change directory to the destination dericroy where Dockerfile exists

~ % docker build -t <tag_name_for_image> .
-> here, i can assign any tag name for image
-> at the the dot ('.') notation is used to build a Docker image based on the contents of the current directory (represented by .)

~ % docker build .
-> it wll create a docker image without a tag name
-> by default its tag name will be <none>


-> Suppose my docker file name is Dockerfile.dev 
-> I can specify a different Dockerfile with the -f (flag) option like the following one
~ % docker build -f Dockerfile.dev .
```

```
Run docker image
~ % docker run -p 5173:5173 -d <tag_name_or_image_id>
5173:5173 defines port_for_app:port_for_image
```

```
Run or Pull images from dockerhub
~ % docker run hello-world
# or
~ % docker pull hello-world
~ % docker run hello-world
```

```sh
Push docker image
# step-1 : create new repository in dockerhub (i.e. rhsajib/chatp-root-react)
# step-2 : build docker image with specific tag
    ~ % docker build -t rhsajib/chatp-root-react:v1.0 ./frontend
# step-3 : push image to dockerhub
    ~ % docker push rhsajib/chatp-root-react:v1.0
```



```
List of all running containers
~ % docker ps
```

```
List of running and stopped containers
~ % docker ps -a
```

```
Start a docker container
~ % docker start <container-id>
```

```
Stop a docker container
~ % docker stop <container-id>
```
```
Remove a container
~ % docker rm <container-id>
```
```
Remove force specified container from this machine
~ % docker rm -f <container-id>
```
```
Remove all containers from this machine
~ % docker rm $(docker ps -a -q)
```

```
Show all images on this machine
~ % docker images
~ % docker images -a
```
```
Remove the specified image from this machine
~ % docker rmi <imagename>
```

```
Remove all images from this machine
~ % docker rmi $(docker images -q)
```

#### Work with interective executable docker container
```
By default exec work on `sh` shell
~ % docker exec -it <container_name> sh
~ % docker exec -it chatp-root-fastapi-1 sh
```


```
To work in zsh shell, inclue the following lines in Dockerfile.
So, we can ensure that zsh is available inside the container.

# Install zsh
RUN apt-get update && apt-get install -y zsh
or for bash,
RUN apt-get update && apt-get install -y bash 


# Set zsh as the default shell
RUN chsh -s /usr/bin/zsh
```
```
After building the Docker image with these changes, you can use zsh inside the container:
~ % docker exec -it chatp-root-fastapi-1 zsh
```



#### Dockerfile 
```
# Dockerfile for react vite

# Use an official Node runtime as a parent image
FROM node:18.4.0-alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install project dependencies
RUN npm i

# Copy the entire project to the container
COPY . .

# Start the development server with npm run dev
CMD [ "npm", "run", "dev"]
```

```
# Dockerfile for FastAPI

# Use an official Python runtime as a parent image
FROM python:3.11.6-alpine

# Set environment variables 
# prevent pip to check its version for upgrade
# ENV PIP_DISABLE_PIP_VERSION_CHECK 1 
# prevent python from writing .pyc files
ENV PYTHONDONTWRITEBYTECODE 1 
# ensure python output is sent directly to the terminal without buffering
ENV PYTHONUNBUFFERED 1

RUN pip install --upgrade pip

# Set the working directory in the container
WORKDIR /app


# Copy the requirements file to the container
COPY requirements.txt .

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir --upgrade -r requirements.txt

# Copy the entire FastAPI application to the container
COPY . .

# which port the app will use
EXPOSE 8000

# Start the FastAPI app with --reload (for development)
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--reload"]

# Start the FastAPI app without --reload (for production)
# CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```


```
##############################################################################
# DOCKER
##############################################################################

docker build -t friendlyname .              # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyname          # Run "friendlyname" mapping port 4000 to 80
docker run -d -p 4000:80 friendlyname       # Same thing, but in detached mode
docker exec -it [container-id] bash         # Enter a running container
docker ps                                   # See a list of all running containers
docker stop <hash>                          # Gracefully stop the specified container
docker ps -a                                # See a list of all containers, even the ones not running
docker kill <hash>                          # Force shutdown of the specified container
docker rm <hash>                            # Remove the specified container from this machine
docker rm -f <hash>                         # Remove force specified container from this machine
docker rm $(docker ps -a -q)                # Remove all containers from this machine
docker images -a                            # Show all images on this machine
docker rmi <imagename>                      # Remove the specified image from this machine
docker rmi $(docker images -q)              # Remove all images from this machine
docker logs <container-id> -f               # Live tail a container's logs
docker login                                # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag         # Upload tagged image to registry
docker run username/repository:tag          # Run image from a registry
docker system prune                         # Remove all unused containers, networks, images (both dangling and unreferenced), and optionally, volumes. (Docker 17.06.1-ce and superior)
docker system prune -a                      # Remove all unused containers, networks, images not just dangling ones (Docker 17.06.1-ce and superior)
docker volume prune                         # Remove all unused local volumes
docker network prune                        # Remove all unused networks


##############################################################################
# DOCKER COMPOSE
##############################################################################


docker-compose build --no-cache
docker-compose up                               # Create and start containers
docker-compose up -d                            # Create and start containers in detached mode
docker-compose build                            # Build all image service
docker-compose down                             # Stop and remove containers, networks, images, and volumes
docker-compose logs                             # View output from containers
docker-compose restart                          # Restart all service
docker-compose pull                             # Pull all image service 
docker-compose config                           # Validate and view the Compose file
docker-compose scale <service_name>=<replica>   # Scale special service(s)
docker-compose top                              # Display the running processes
docker-compose run -rm -p 2022:22 web bash      # Start web service and runs bash as its command, remove old container.

##############################################################################
# DOCKER SERVICES 
##############################################################################


docker service create <options> <image> <command>   # Create new service
docker service inspect --pretty <service_name>      # Display detailed information Service(s)
docker service ls                                   # List Services
docker service ps                                   # List the tasks of Services
docker service scale <service_name>=<replica>       # Scale special service(s)
docker service update <options> <service_name>      # Update Service options


##############################################################################
# DOCKER STACK 
##############################################################################


docker stack ls                                 # List all running applications on this Docker host
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker stack services <appname>                 # List the services associated with an app
docker stack ps <appname>                       # List the running containers associated with an app
docker stack rm <appname>                       # Tear down an application


##############################################################################
# DOCKER MACHINE
##############################################################################


docker-machine create --driver virtualbox myvm1                           # Create a VM (Mac, Win7, Linux)
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm1  # Win10
docker-machine env myvm1                                                  # View basic information about your node
docker-machine ssh myvm1 "docker node ls"                                 # List the nodes in your swarm
docker-machine ssh myvm1 "docker node inspect <node ID>"                  # Inspect a node
docker-machine ssh myvm1 "docker swarm join-token -q worker"              # View join token
docker-machine ssh myvm1                                                  # Open an SSH session with the VM; type "exit" to end
docker-machine ssh myvm2 "docker swarm leave"                             # Make the worker leave the swarm
docker-machine ssh myvm1 "docker swarm leave -f"                          # Make master leave, kill swarm
docker-machine start myvm1                                                # Start a VM that is currently not running
docker-machine stop $(docker-machine ls -q)                               # Stop all running VMs
docker-machine rm $(docker-machine ls -q)                                 # Delete all VMs and their disk images
docker-machine scp docker-compose.yml myvm1:~                             # Copy file to node's home dir
docker-machine ssh myvm1 "docker stack deploy -c <file> <app>"            # Deploy an app
```
