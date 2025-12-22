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


