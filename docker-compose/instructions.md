
# Docker Compose

Docker compose is a way to create and manage multi-container applications.  It works using a YAML configuration file called `compose.yaml` or `docker-compose.yml`. 

Generally using docker compose is a multi-step process:

- Define each of your containers using a `Dockerfile`
- Push those containers to a repository
- Define a docker compose file
- use the file via the `docker compose` command.  

This is an example `compose.yaml` file.  

```
services:
  db:
    image: mariadb:10.6.4-focal
    command: '--default-authentication-plugin=mysql_native_password'
    volumes:
      - mariadb_data:/var/lib/mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=somewordpress
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - MYSQL_PASSWORD=wordpress
    expose:
      - 3306
      - 33060
  wordpress:
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - 8081:80
    restart: always
    environment:
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_USER=wordpress
      - WORDPRESS_DB_PASSWORD=wordpress
      - WORDPRESS_DB_NAME=wordpress
volumes:
  mariadb_data:
  wordpress_data:
```

It defines two containers `db` and `wordpress` both defined under `services:`. It also defines two named volumes `mariadb_data` and `wordpress_data`, which are created outside of the lifecycle of any one container and can be referenced by the containers defined within the compose file, in this example `db`.  

Go ahead and create a file in your current working folder called `compose.yaml` and paste in the above contents.  Then to use this file run a `docker compose up -d`.  `up` is how we tell docker compose to bring up the things defined in the file, and `-d` directs compose to run in detached mode (which works in the same way as it does it containers).  

browse to ```http://localhost:8081``` and complete the installation process of wordpress. You will need to select a language, click next, set a site title `All the cats`, set an admin user `admin`, note down the auto generated password (you will need this a few times), enter your email and click `Install Wordpress`.  

Then ..Login, Click `posts` then `Add new` and create a new post with some images, use the `+` to add a gallery, then add some test images, the details don't matter.  Click `publish` and then `publish` again.  Then click `view post` & make sure you can see the site ok...  

Run a ```docker compose down``` to stop all the containers and then do a, ```docker ps -a```, notice the containers are fully removed?  
run a ```docker volume ls``` and note how the named volumes still exist?  
   
The containers just created by docker compose are now gone.   
Run a ```docker compose up -d``` .. and watch what compose does, it only recreates what's missing. Docker compose is smart enough to always create what's required to make what is container in the docker compose file `true` whether that means creating everything in it, or just a small section.  
browse to ```http://localhost:8081``` and notice how the install is finished ? no configuration is needed ? and you see your blog post? that's because the named volumes are used, which contain the database and the image data from wordpress. Even though the containers were deleted and recreated the data persisted because it's stored in volumes which exist outside of the lifecycle of the container.


# Cleanup

Run a `docker compose down` to stop and delete any containers.  
Run a `docker volume ls` to list all volumes and delete the named ones created by compose using `docker volume rm <VOLNAME>`  



