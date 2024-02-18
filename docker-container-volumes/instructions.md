# Preparation

Ensure you have completed the previous demonstration lesson on environment variables and understand all aspects of that demonstration. Recreate the `phpmyadmin` container with the following command:

```sh
docker run --name phpmyadmin -d -p 8081:80 -e PMA_ARBITRARY=1 phpmyadmin/phpmyadmin
```

# Bind Mounts

Bind mounts allow us to mount a file or directory from the host machine into a container. This approach has both advantages and disadvantages, but it can be particularly useful if you need to access files on the container host, share files between containers, or between containers and other compute services.

In this example, we will create a bind mount for the MariaDB data directory within the container and map it to a directory on the Docker host.

There are two ways to achieve this: using the `-v` option or the `--mount` option. Both options accomplish the same task but are formatted differently.

As a reminder, in the environment variables demonstration lesson, we created a MariaDB container using the following command:

```sh
docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password
```

The MariaDB data was stored within the container in the `/var/lib/mysql` directory.

First, on your local machine/Docker host, create a directory for the MariaDB data, and name it `mariadb_data`.

- Navigate to a directory where you are comfortable creating files and directories, such as your home directory or a temporary directory.
- For simplicity, let's assume you're using your home directory.
- On Windows, navigate with `cd %HOMEDRIVE%`. On macOS/Linux, use `cd ~`.
- Create a directory called `mariadb_data` with `mkdir mariadb_data`.

### macOS / Linux

*Choose one of the following commands, as both perform the same action:*

```sh
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -e MYSQL_PASSWORD=wordpress \
 --mount type=bind,source="$(pwd)"/mariadb_data,target=/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

*Or:*

```sh
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -e MYSQL_PASSWORD=wordpress \
 -v "$(pwd)"/mariadb_data:/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

### Windows

*Choose one of the following commands, noting that bind mounts often require specific configuration on Windows Docker hosts and can be challenging to set up:*

```sh
docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress --mount type=bind,source=%cd%/mariadb_data,target=/var/lib/mysql -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password
```

*Or:*

```sh
docker run --name db -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress -v %cd%/mariadb_data:/var/lib/mysql -d mariadb:10.6.4-focal --default-authentication-plugin=mysql_native_password
```

### macOS / Linux & Windows

In all the above cases, navigate into the `mariadb_data` directory and list its contents. You should see all files and directories created by the bind mount. Any data stored in `/var/lib/mysql` within the container is also stored in this directory on the host. Changes made in one location are reflected in the other.

# Volumes

Volumes are the preferred method of adding storage to Docker containers outside of a container's lifecycle. Managed entirely by Docker, volumes work seamlessly on Windows container hosts.

To create a volume, run:

```sh
docker volume create mariadb_data
```

List volumes with `docker volume ls`, inspect a volume with `docker volume inspect mariadb_data`, and delete a volume with `docker volume rm mariadb_data`. Verify removal with `docker volume ls`.

If you start a container and specify a non-existing volume, Docker will automatically create it for you.

*Choose one of the following commands, as both achieve the same result:*

```sh
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -e MYSQL_PASSWORD=wordpress \
 --mount source=mariadb_data,target=/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

*Or:*

```sh
docker run \
 --name db \
 -e MYSQL_ROOT_PASSWORD=somewordpress \
 -e MYSQL_DATABASE=wordpress \
 -e MYSQL_USER=wordpress \
 -e MYSQL_PASSWORD=wordpress \
 -v mariadb_data:/var/lib/mysql \
 -d \
 mariadb:10.6.4-focal \
 --default-authentication-plugin=mysql_native_password
```

Note how `docker volume ls` shows the newly created volume. Stopping the container with `docker stop db` and deleting it with `docker rm db` leaves the volume intact. Data within the volume persists beyond the container's lifecycle, allowing reuse in new containers.

# Cleanup

To clean up, run the following commands:

```sh
docker stop db
docker rm db
docker stop phpmyadmin
docker rm phpmyadmin
docker volume rm mariadb_data
```

This ensures all containers and the volume are properly removed.