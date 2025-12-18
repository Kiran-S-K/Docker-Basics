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
  