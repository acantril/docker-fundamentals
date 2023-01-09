
# Working with existing docker images

## Verify what's currently on the dockerhost
```docker ps```  
```docker images```

## Get an existing image from dockerhub 
- https://hub.docker.com/  
- search for "containerofcats"  
- click "acantril/containerofcats"  
 
```docker pull acantril/containerofcats```   

*notice how it's pulling a few fs layers ..*  

## Working with the docker image & containers locally
  
```docker images -a```  
```docker inspect <imageID>```  

*Run a container from the downloaded docker image*    
```docker run -p 8081:80 acantril/containerofcats```  
browse to it - see the cats? :)    
notice how the terminal is connected to the container  
if we ctrl+c to exit.. the container stops  
because the terminal is/was attached to the container  

```docker ps```  

we can run the container detached (-d), the terminal will drop back to the command prompt but the container is still running  

```docker run -p 8081:80 -d acantril/containerofcats```
    
*now it's running and we can continue working and access the container.*

this command will show the port mappings, host:container (i'll cover this later in the course)   

```docker port <CONTAINERID>```  

we can execute commands inside the container, dont worry if you don't see the qemu part it depends on your architecture  
 
```docker exec -it <CONTAINERID> ps -aux```  

You can even run a shell inside a running container    
```docker exec -it <CONTANERID> sh```  
	
we can restart the container    
```docker restart <CONTAINDERID>```  

stop it  
```docker stop <CONTAINERID>```  
```docker ps```  
```docker ps -a```  

start it again  
```docker start <CONTAINERID>```  
```docker ps```  

we can show logs  
```docker logs <CONTAINERID>```  
add timestamps  
```docker logs <CONTAINERID> -t```  

## Let's cleanup all the things from this demo
Stop the running container    
```docker stop <CONTAINERID>```  
Remove the stopped container  
```docker rm <CONTAINERID>```  
List the images we have locally  
```docker images```  
Remove the image we pulled from dockerhub  
```docker rmi <IMAGEID>```  
	









