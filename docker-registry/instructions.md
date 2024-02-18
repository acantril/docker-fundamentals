First, we pull an image from Docker Hub to our local Docker host. This involves pulling the `latest` image from the `containerofcats` repository in the `acantril` account on Docker Hub.

```sh
docker pull acantril/containerofcats:latest
```

Now, we will create an image and upload it to Docker Hub.
First, go to [https://hub.docker.com](https://hub.docker.com) and create an account. You will need to note down your account name.

Create a repo on Docker Hub called `newcontainerofcats`.

Then, you will need to log in to Docker Hub. If you use `username` and `password` to log in, you can enter your password when prompted in the step below. If you use `username`, `password`, and `MFA`, you will need to go to [https://hub.docker.com/settings/security](https://hub.docker.com/settings/security) and generate a token to input **instead** of your password below.

Log in to Docker Hub using the command below:

```sh
docker login --username=YOUR_USER
```

**You will either enter your password when prompted or your token (see above).**

Move into the repo/docker-registry folder and build the `newcontainerofcats` image using the command below:

```sh
docker build -t newcontainerofcats .
```

Verify the image is created using the command below, and note down the image ID:

```sh
docker images
```

To push this image to Docker Hub, we need to tag it with our hub username and repo name.

```sh
docker tag IMAGEID YOUR_USER/newcontainerofcats:latest
```

Then, we can push this image to Docker Hub:

```sh
docker push YOUR_USER/newcontainerofcats:latest
```

Then, browse to Docker Hub and verify that it's visible.
