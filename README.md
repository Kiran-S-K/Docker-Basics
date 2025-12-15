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

