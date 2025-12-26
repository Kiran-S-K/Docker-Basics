## Docker basics
Docker is a containerization platform that allows you to package an application along with its dependencies, libraries, and configuration into a single unit called a container, so that it runs the same on any system.  
Basically it solves the problem. "It runs on my computer but not yours?"  
So mainly docker has 2 parts: container and image.  
-Docker container: single unit which contains application and dependencies.  
-Docker image: A Docker image is a read-only blueprint that contains everything needed to run an application.  
That includes:  
-the application itself  
-its dependencies  
-libraries  
-environment setup  
-default commands  
Image is shared with other users through which they can run the containers, and the containers are unique, Image is like a exe file and containers are like the game files which are the same game but different copies of it.  
Resources of the system is used by container and not image.  
## Docker commands  
-docker pull IMAGE_NAME (only pulls image and doesnt create a container)  
-docker images  
-docker run IMAGE_NAME (it creates a container for the image)  
-docker run -it IMAGE_NAME (it-interactive)  
-docker stop CONT_NAME or CONT_ID  
-docker start CONT_NAME or CONT_ID  
-docker ps -a (lists all container ID, images and info, -if docker ps is used then it shows only the running containers)  
-docker rmi IMAGE_NAME (removes or destroys an image)  
-docker rm CONT_NAME (removes or destroys a container)  
-docker pull IMAGE_NAME:version  
-docker run -d IMAGE_NAME (d-detach mode, normally all containers run in attach mode)  
-docker run --name CONT_NAME -d IMAGE_NAME (to create custom name for container)  
## Port Binding
Port binding connects a port inside a container to a port on your computer (host).  
Containers are isolated.  
Services inside them are not accessible unless you bind ports.  
# You do NOT need port binding when:  
-You are just running a container  
You are:  
-Executing commands inside it  
-Using it as a tool (Ubuntu, Python, Node, etc.)  
-Not exposing a service to the outside  
Ex: docker run -it ubuntu  
# Command for port binding:
-docker run -p HOST_PORT:CONTAINER_PORT IMAGE_NAME   
# NOTE: 
Once a container is bound to a host port(for ex:8080) another container cannot be given the same host port.  
## Troubleshoot Commands
-docker logs CONT_ID (gives logs of a specific container so that we can identify any probelm related to it)  
-docker exec -it CONT_ID/bin/bash (exec lets us run commands on an alerady running container)  
-docker exec -it CONT_ID/bin/sh  
# NOTE:
In gitbash terminal use these as the commands above wont run.  
-docker exec -it CONT_ID "bash"  
-docker exec -it CONT_ID "sh"  
Then use 'env' to display the details and use 'exit' to exit the command.  
## Developing with docker 
# docker network
Docker network is a virtual network that lets container talk to each other
docker network ls  
docker network create NETWORK_NAME  

So now we will use an example of mongoDB for docker network.  
For pulling or running mongoDB image, we need:  
-to run in detach mode (-d)  
-port binding to the specific port given in the docker hub (27017) [-p 27017:27017]  
-then some nickname (--name mongo)  
-then we need it to run inside our isolated network (--network mongo-netowrk)  

Then some environment variables -e :  (we will use name as admin and password as qwerty for this example)  
-e ROOT_USER_NAME=admin  
-e ROOT_PASSWORD=qwerty  

Built a custom Node.js Docker image using an official Node base image, containerized an Express server, exposed port 5050, and connected it to MongoDB using Docker networking.  
Your project must look like this:  
docker-testapp/
├── server.js
├── package.json
├── package-lock.json
├── node_modules/        
├── public/
└── Dockerfile


Then create a dockerfile which contains:  
# Use official Node.js image
FROM node:18-alpine  

# Set working directory inside container
WORKDIR /app  

# Copy package files first (for caching)
COPY package*.json ./  

# Install dependencies
RUN npm install  

# Copy rest of the application
COPY . .  

# Expose the port your app runs on
EXPOSE 5050  

# Start the application
CMD ["node", "server.js"]  
Then build the docker image:  
docker build -t node-app . (-t is to name an image, "." current directory as build context)  
# If any old container exists with same name:
docker rm node-app or docker remove -f node-app  
Then run the container and put it to local network(mongo-network)  
docker run -d \  
  --name node-app \  
  --network mongo-network \  
  -p 5050:5050 \  
  node-app  
Then finally test api in browser whether its running or not:  
http://localhost:5050/getUsers  
# Docker compose
It is a tool for defining and running multi-container applications.  
We run the container from a file which has .yaml(yet another markup language) extension instead of running on terminal. 
# NOTE: In yaml file all the containers are by default in the same network, so no need of explicitly mentioning the network.
Now lets take the example of mongo container:  
_In terminal_:  
docker run -d \  
-p 27017:27017 \  
--name mongo \  
--network mongo-network \  
-e MONGO_INITDB_ROOT_USERNAME=admin \  
-e MONGO_INITDB_ROOT_PASSWORD=qwerty \  
mongo  
_*But in yaml file, ex: compose.yaml*_  
version: "3.8" //You can skip this too but for specific version cant  
services: *all containers 
  mongo:  
    image: mongo  
    ports:  
      -27017:27017  
    environment:  
       MONGO_INITDB_ROOT_USERNAME:admin  
       MONGO_INITDB_ROOT_PASSWORD:qwerty  
# NOTE: Idendation is important in yaml file  
_*2 important docker compose commands*_  
docker compose -f fileName.yaml up -d (up = run+start)  
docker compose -f fileName.yaml down (down = stop + rm)  
## Dockerizing our app
It is the process of packaging an application and its environment into an image from which we can form containers and run consistently on any device whihch has docker.  
So, an important term related to dockerization:  
# Dockerfile 
A text file which contains step by step instructions to build a docker image.  
_*Important dockerfile instructions*_  
-FROM: Every image we build is based on a base image.FROM specifies the base images on which my Docker image is built.Base image is a pre-built docker image that contains OS,runtime,npm,system libraries.  
*Syntax*: FROM base_image:version , FROM python:3.11  
-WORKDIR: Sets the current working directory for the container, its like using cd command. If WORKDIR /app then docker assumes every command runs inside /app. If no workdir then docker assumes (cd ..) which is root directory which is bad and messy and dangerous.  
-COPY: copies the files from machine into the current directory of the container. (COPY . .) means first . -> current folder on host copied to second . -> current folder inside container (ex: WORKDIR /app)  
-RUN: It executes a command while the image is being built and saves the result into the image. _Important_: RUN command runs the command during build time and then freeze the result into the image.Can have multiplr RUN commands.  
-CMD: Defines what runs during container starts.CMD runs at runtime and not during buildtime.Can have only one CMD per dockerfile.(Mental mode: After everything is built, start the app like this.)  
-EXPOSE: Documents which port the app listens on.  
(EXPOSE 5050)  
VERY IMPORTANT  
-Does NOT open the port (actual port opening happens via: [docker run -p 5050:5050])  
-Does NOT publish the port  
-Just documentation for Docker & Compose  
(Mnetal mode: Putting a label on door saying,this service talks on port 5050)  
-ENV: Sets environment variables inside the container.   
# Docker volumes
Volumes are persistent data stores for containers.  
-v host_path:container_path (both the paths must be absolute, i.e full path starting from root)  
## You can create custom volumes:
-docker volume create vol_name (to create volume,these are named volumes) *[by default the volumes are created in C:\ProgramData\docker\volumes] as we do not mention the location*  
-docker volume rm vol_name (to remove volume)  
-docker volume ls (to see all created volumes)  
_custom docker volumes which are created are not attached with any containers_  
# *So to attach the container with the container we use:*  
_For named volumes:_  
- docker run -v vol_name:cont_directory (if vol_name doesnt exists, docker auto creates the volume and runs)  
_For anonymous volumes:_  
- docker run -v mount_path  
_For bind mount:_  
- docker run -v host_directory:cont_directory  
-docker volume prune (it prunes all anonymous volumes and volumes which are not attached to any containers)  
