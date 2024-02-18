# Prerequisites

Ensure you have cloned or downloaded the course GitHub repository to your local machine.

Locate the `build-a-simple-containerized-application` folder in the course repository.

# Application 1 - 2048

![2048-diagram](files/2048-game.png)
*Diagram shows the flow - from the Dockerfile to the fun playing game!*

## Creating a 2048 Docker Image

- Move into the `app1-2048` folder.
- Open the `Dockerfile` in a text editor of your choice.
- Notice how it contains `5` lines: `FROM`, `LABEL`, `COPY`, `EXPOSE`, and `CMD`. When you build the Docker image in the next step, pay attention to how many layers are created.

This Dockerfile uses the latest version of the `nginx` image (which includes a pre-configured installation of NGINX).

[https://hub.docker.com/_/nginx](https://hub.docker.com/_/nginx)

It sets the metadata `maintainer`. It then copies all of the `2048/` folder from the local Docker host/client machine into the `/usr/share/nginx/html` folder within the image.

Finally, we use the `EXPOSE` directive to inform users of this image that the application is running on `tcp/80` and use the `CMD` statement to set the initial process for containers running from this image.

To build the image, from the `app1-2048` folder, we run the Docker build command. We use `-t` to tag the image with `dockerized-2048` and use `.` to set the working directory for the command. Any paths specified in the Dockerfile will use `.` as the starting point, which is the working directory.

```sh
docker build -t dockerized-2048 .
```

Notice how 2 layers are created in the Docker image:

- The first layer is the base image `nginx:latest`.
- The last layer is created based on the `COPY` statement to copy in the application files from `./2048`.

## Running a 2048 Docker Container

Run `docker images` to show all images on the Docker Host. Locate the `dockerized-2048` image you just created. This image is running an application using `tcp/80`, so we need to use the `-p` CLI option to run this on an alternative Docker host port (this will be discussed in detail later in the course).

Run a Docker container of 2048 using:

```sh
docker run -d -p 8081:80 dockerized-2048
```

This will run in detached mode, so your terminal should return to the prompt immediately.

Verify the container is running with `docker ps`. Confirm the port mapping with `docker port <CONTAINERID>`.

Browse to `http://localhost:8081`.

Play the game :)

## Cleanup

Run `docker stop <CONTAINERID>` to stop the 2048 container app. Run `docker rm <CONTAINERID>` to remove the 2048 container.

# Application 2 - Container of Cats

This Dockerfile is intentionally less efficient than app1 above. I want you to understand WHY it's less efficient.

## Creating a Container of Cats Docker Image

- Move into the `app2-containerofcats` folder.
- Open the `Dockerfile` in a text editor of your choice.

Let's review this `Dockerfile` and specifically identify how it's different from the previous `app1`.

- First, it uses a different base image. Instead of `nginx:latest`, which is specifically designed for simple `nginx` web use-cases, this uses `ubi8`, which is a universal Red Hat 8 image (general purpose).
- This image is also larger than other lean images such as `alpine` and `nginx`.
- Because this is a general-purpose image, we have to install a web server (apache2) in this case via the `RUN` statement. This creates a layer.
- Next, there are 2 `COPY` statements which copy in `index.html` and `containerandcat*.jpg` media files into the image. These two statements create _2_ additional layers.

This Dockerfile and resultant image are more generalized. This results in a larger image and containers which consume more resources.

To create a Docker image for the `containerofcats` application, we run this command:

```sh
docker build -t containerofcats .
```

*Notice how much longer it takes to create, compared to the 2048 image, even though it's a similar architecture: a web server & some files.*

## Running a Containerofcats Docker Container

Run `docker images` to show all images on the Docker Host. Locate the `containerofcats` image you just created. This image is running an application using `tcp/

80`, so we need to use the `-p` CLI option to run this on an alternative Docker host port (this will be discussed in detail later in the course).

Run a Docker container of containerofcats using:

```sh
docker run -d -p 8081:80 containerofcats
```

This will run in detached mode, so your terminal should return to the prompt immediately.

Verify the container is running with `docker ps`. Confirm the port mapping with `docker port <CONTAINERID>`.

Browse to `http://localhost:8081`.

## Cleanup

Run `docker stop <CONTAINERID>` to stop the containerofcats container app. Run `docker rm <CONTAINERID>` to remove the containerofcats container.

