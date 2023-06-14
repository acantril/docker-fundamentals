
# Setup phpmyadmin
this is a simple mySQL or mariaDB management app, we map container port 80 to docker host port 8081, we call it phpmyadmin.  
the -e `PMA_ARBITRARY=1` lets us specify the server to use each time via the UI, vs statically setting this.  

Run
```docker run --name phpmyadmin -d -p 8081:80 -e PMA_ARBITRARY=1 phpmyadmin/phpmyadmin```

This will create a container running the `phpmyadmin` DB management application.  

# Create a mariaDB container using ONLY environment variables

first we need to pull down the mariaDB image.  
```docker pull mariadb:10.6.4-focal```
lets inspect it and review all metadata.  
```docker inspect mariadb:10.6.4-focal```

Now let's run a container and note how we use environment variables.  

- using `-e` we specify a key=value pair
- it's convention for NAMES to be in caps
- in this case we create `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, `MYSQL_DATABASE` and `MYSQL_USER`
- The container is configured to accept these variables and take action on them
- `MYSQL_ROOT_PASSWORD` sets the mariaDB root password
- `MYSQL_DATABASE` creates a database with the name of the env variable value `wordpress` in this example
- `MYSQL_USER` creates a mariaDB user
- `MYSQL_PASSWORD` creates a password for that user
- `--default-authentication-plugin=mysql_native_password` is an argument for the process running in the docker container, extra options. 
  
```docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_PASSWORD=wordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password```

confirm the docker container is running with a 
```docker ps -a```

and get the container ID... note this down as `MARIADB_ID`  
run the command below to show the metadata for the running container for mariaDB.  
```docker inspect MARIADB_ID ```


look for "IPAddress": "XXXXXXXXXX", this is the internal docker network IP that the DB container is running on. note this down as `DB_IP`  

open http://localhost:8081 to access phpmyadmin
enter `DB_IP` for server  
enter `root` for username  
enter `somewordpress` for password  

You are now accessing the db container, from the phpmyadmin container

- Go to `User Accounts` and confirm a wordpress user has been created.
- Look on the menubar on the left and confirm there is a `wordpress` database

Now we're going to explore the container.  

run a ```docker exec -it db bash``` to go into the container via a bash shell.  
run a ```df -k``` and note how there are no externally mapped drives/mounts. All storage that this container uses, is within this container. 
This means the storage is linked to the lifecycle of this container, if the container is deleted, so is the storage.  

The mariaDB service stores its data in `/var/lib/mysql` so let's check that out.  
run a ```cd /var/lib/mysql``` and then ```ls -la```  
this is the `data` for the database service ... inside the container.

This is a limitation we're going to fix in the next demo, where we will look at configuring separate storage for our container.  

That's it for this lesson

# Cleanup

run the following two comments to stop and delete the mariaDB container..  

```
docker stop db
docker rm db
```

lets also for now delete the phpmyadmin container (we can easily launch this again in the next demo)

```
docker stop phpmyadmin
docker rm phpmyadmin
```


