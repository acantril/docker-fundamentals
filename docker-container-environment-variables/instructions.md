# Setup phpMyAdmin
This is a simple MySQL or MariaDB management app. We map the container port 80 to the Docker host port 8081 and call it phpMyAdmin.
The `-e PMA_ARBITRARY=1` option allows us to specify the server to use each time via the UI, versus statically setting this.

## Run

```sh
docker run --name phpmyadmin -d -p 8081:80 -e PMA_ARBITRARY=1 phpmyadmin/phpmyadmin
```

This command creates a container running the `phpMyAdmin` DB management application.

# Create a MariaDB Container Using ONLY Environment Variables

First, we need to pull down the MariaDB image.
```sh
docker pull mariadb:10.6.4-focal
```
Let's inspect it and review all metadata.
```sh
docker inspect mariadb:10.6.4-focal
```

Now, let's run a container and note how we use environment variables:

- Using `-e`, we specify a key=value pair.
- It's convention for names to be in uppercase.
- In this case, we create `MYSQL_ROOT_PASSWORD`, `MYSQL_PASSWORD`, `MYSQL_DATABASE`, and `MYSQL_USER`.
- The container is configured to accept these variables and take action on them.
- `MYSQL_ROOT_PASSWORD` sets the MariaDB root password.
- `MYSQL_DATABASE` creates a database with the name of the environment variable value `wordpress` in this example.
- `MYSQL_USER` creates a MariaDB user.
- `MYSQL_PASSWORD` creates a password for that user.
- `--default-authentication-plugin=mysql_native_password` is an argument for the process running in the Docker container, adding extra options.

```sh
docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password
```

Confirm the Docker container is running with:
```sh
docker ps -a
```

Run the command below to show the metadata for the running container for MariaDB.
```sh
MARIA_DB_CONTAINER_ID=$(docker ps | grep mariadb | awk '{print $1}')
docker inspect ${MARIA_DB_CONTAINER_ID}
```

Look for `"IPAddress": "XXXXXXXXXX"`, this is the internal Docker network IP that the DB container is running on. Note this down as `DB_IP`.

Open `http://localhost:8081` to access phpMyAdmin.
Enter `DB_IP` for the server.
Enter `root` for the username.
Enter `somewordpress` for the password.

You are now accessing the DB container from the phpMyAdmin container.

- Go to `User Accounts` and confirm a WordPress user has been created.
- Look on the menu bar on the left and confirm there is a `wordpress` database.

Now we're going to explore the container.

Run a ```docker exec -it db bash``` to go into the container via a bash shell.
Run a ```df -k``` and note how there are no externally mapped drives/mounts. All storage that this container uses is within this container.
This means the storage is linked to the lifecycle of this container; if the container is deleted, so is the storage.

The MariaDB service stores its data in `/var/lib/mysql`, so let's check that out.
Run a ```cd /var/lib/mysql``` and then ```ls -la```.
This directory contains the data for the database service inside the container.

This is a limitation we're going to fix in the next demo, where we will look at configuring separate storage for our container.

That's it for this lesson.

# Cleanup

Run the following two commands to stop and delete the MariaDB container:

```sh
docker stop db
docker rm db
```

Let's also, for now, delete the phpMyAdmin container (we can easily launch this again in the next demo):

```sh
docker stop phpmyadmin
docker rm phpmyadmin
```
