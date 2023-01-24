
First we pull an image from Docker Hub to our local docker host.  This is pulling the `latest` image, from the `containerofcats` respsitory in the `acantril` account on Docker Hub.  

```docker pull acantril/containerofcats:latest```

Now we will create an image and upload it to Docker Hub.  
First, go to https://hub.docker.com and create an account. You will need to note down your account name.

Create a repo on dockerhub called `newcontainerofcats` .  

Then, you will need to login to Docker Hub. if you use use `username` and `password` to login, then you can enter your password when prompted in the step below. if you use `username`, `password` and `MFA` you will need to go to https://hub.docker.com/settings/security and generate a token and input that **instead** of your password below.   

Login to Docker Hub using the command below
```docker login --username=YOUR_USER```
**you will either enter your password when prompted or your token (see above)**

Move into the repo/docker-registry folder and build the `newcontainerofcats` image using the command below

```docker build -t newcontainerofcats .```

Verify the image is created using the command below, and note down the image ID   

```docker images```

To push this image to Docker Hub we need to tag it with our hub username and repo name.  

```docker tag IMAGEID YOUR_USER/newcontainerofcats:latest```

Then we can push this image to dockerhub

```docker push YOUR_USER/newcontainerofcats:latest```

Then browse to docker hub and verify that it's visible.
