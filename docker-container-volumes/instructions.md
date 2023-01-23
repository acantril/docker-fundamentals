# Prep

Make sure you have done the previous demo lesson on environment variables and understand all parts of that demo.  
Recreate the `phpmyadmin` container via the command below

```docker run --name phpmyadmin -d -p 8081:80 -e PMA_ARBITRARY=1 phpmyadmin/phpmyadmin```

# Bind Mounts

Bind mounts allow us to mount a file or directory on the host machine into a container. This has pros and cons but it can be useful if you need to see this files on the container host, or access a shared collection of files between container, or between containers and other compute services.  

In this example, we're going to create a bind mount for the mariaDB data folder in the container, and map it to a folder on the docker host.  

we can do this is two ways, using the `-v` option or the `--mount` option. They both do the same thing, but they look different.

As a reminder, in the environment variables demo lesson, we created a mariaDB container using this command:-

```docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password```

and the mariaDB data was stored in the container in the `/var/lib/mysql` folder.  

first, on your local machine/docker host we will create a folder for the mariaDB data, and we will call it `mariadb_data`. 

- move into a folder where you are ok creating files and folders, either your home folder or a tmp folder.  
- I'll assume your home folder
- windows `cd %HOMEDRIVE%` macos/Linux `cd ~`
- create a folder called `mariadb_data`
- `mkdir mariadb_data`

### macOS / Linux
*both of these do the same thing, pick one*

```
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_PASSWORD=wordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 --mount type=bind,source="$(pwd)"/mariadb_data,target=/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```
*both of these do the same thing, pick one*
```
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_PASSWORD=wordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -v "$(pwd)"/mariadb_data:/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

### Windows
*both of these do the same thing, pick one*
*bind mounts often requires specific configuration on windows docker hosts*
*it's often tricky to get it working*

```docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress --mount type=bind,source=%cd%/mariadb_data,target=/var/lib/mysql -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password```

```docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -v %cd%/mariadb_data:/var/lib/mysql -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password```


### macOS / Linux & Windows

In all of the above cases, move into the `mariadb_data` folder and do a listing..  
You should be able to see all the files and folders created by the bind mount into the container. Any data stored in the `/var/lib/mysql` folder in the folder, is stored in this folder on the host. Any changes made to the `/var/lib/mysql` in the container will be reflected in this folder. Any changes made to this folder, will be reflected in the `/var/lib/mysql` folder in the container.  

# Volumes

Volumes are the prefered way to adding storage to docker containers outside of the lifecycle of a container. They are managed entirely by docker and work flawlessly on windows container hosts.  

to create a volume run

```docker volume create mariadb_data```
you can list any volumes via the ```docker volume ls``` command.  
you can review full metadata for a volume via the ```docker volume inspect mariadb_data``` command.   
you can delete a volume via the ```docker volume rm mariadb_data``` command.   
and verify it's been removed via the ```docker volume ls``` command.  

if you run a container and specify a volume which doesn't already exist, it will create this volume for you.   

*both of these do the same thing, pick one*

```
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_PASSWORD=wordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 --mount source=mariadb_data,target=/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

```
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_PASSWORD=wordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -v mariadb_data:/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

notice via ```docker volume ls``` how this has automatically created a volume.  if we stop the container with ```docker stop db``` and then delete it with ```docker rm db``` notice how the volume still exists ```docker volume ls``` ?

if we start up the container again, we can use the same volume and any data stored within it persists, so data can live on beyond the lifecycle of the container.  

```
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_PASSWORD=wordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -v mariadb_data:/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

get the internal container ip via ```docker inspect db``` and then browse to https://localhost:8081 and login with that ip, `root` and `somewordpress`, notice how it works fine ?   


# Cleanup

```
docker stop db
docker rm db
docker stop phpmyadmin
docker rm phpmyadmin
docker volume rm mariadb_data
```



