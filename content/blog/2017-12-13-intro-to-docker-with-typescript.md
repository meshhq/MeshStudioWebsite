---
title: Intro to Docker with TypeScript
sub_title: How to set up and run a Docker Container of a TypeScript application.
tags: []
date: 2018-02-13T13:47:59-08:00
publishdate: 2018-02-13T13:47:59-08:00
hero_image: /images/blog/2018-01-29-docker-compose-and-typescript/docker-compose-hero.png
author:
  name: Reid Weber
  url: https://medium.com/@reidweber_34407
  mail: reid@meshstudio.io
  avatar: "https://avatars2.githubusercontent.com/u/8824846?s=460&v=4"
---

## Intro to Docker with TypeScript

Over the past few years Docker has emerged as one of the most efficient ways to manage and deploy cloud-based web applications☁️ 💻 . While containers have been around since 2008, Docker helped them go mainstream, fueling the Virtual Machine vs. Container argument.

### What are Docker Containers? (and why should I care?)

In short, a container is an operating system agnostic environment that hosts a fully functional application. Docker uses a virtual machine under the hood which allows developers to declare runtime dependencies via a Dockerfile.

Prior to Docker, developers were forced to manually bootstrap runtime dependencies in order to leverage a virtual machine. In contrast, a container ships with its runtime specified. In fact, a container is intended to be comprised of only its runtime dependencies and the application code. We’ll talk a bit more later about how your Docker parent image will allow you to create containers that already have dependencies like Node and Typescript installed.

A container is able to ship without a dedicated operating system since the container’s VM will handle all the operating system translation needed. Developers can rest assured that their applications will run the same, regardless of the local machine’s operating system or specifications.

For more info (and nifty graphics) go [here](https://docs.docker.com/get-started/).

### Getting Started

First you’ll have to go thru the installation process. Click the following for [macOS](https://docs.docker.com/docker-for-mac/install/) or [Windows](https://docs.docker.com/docker-for-windows/install/).

__Note: You may have noticed the different pages in the docs for Docker CE and Docker EE. This is the Docker Community Edition vs. the Enterprise Edition. For this tutorial we don’t need to worry about EE. For the CE there are two options: stable and edge. We’ll be using stable since we aren’t going worry about the latest and greatest features being built out by the community (See the Docker CE repo here).__

Once you have Docker installed, make sure the Docker application is running by checking for the little whale on your top bar (I’m using a mac for this tutorial).

| ![See if Docker is running.](/images/blog/2017-12-13-intro-to-docker-with-typescript/docker-running.png) |
|:--:|
|If Docker is not running and you try to run a Docker command, you’ll get an error in the console that looks like this: “docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.”|

If you don’t have a Docker account, sign up at [cloud.docker.com](https://cloud.docker.com/). Then run `docker login` and enter your basic auth credentials.

Pull up the command line and run `docker run hello-world`; you should get this output:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
 ```

What we’re doing here is fetching the `hello-world` image from DockerHub and running it on our local machine. This image is pretty basic since its only command is to print out the info above to the console (the exact steps are outlined in the above output).

Now let’s run a different, more complex, image. Run `docker run -it ubuntu bash`. This will pull the latest Ubuntu image and run it as a container on your machine. The `bash` argument at the end of the command allows us to open a command line session inside the new container we created. You can now issue commands to the new container from your existing console. If you run `uname` you can see that the console prints `"Linux"` — this means that your container is now running on the Docker VM (which has a virtual Linux kernel). Exit the Ubuntu shell by typing exit and pressing enter. If you run `uname` again, you’ll now see the “real” kernel type your machine is running on (in my case it’s `"Darwin"`).

### Creating the Dockerfile

Next we’re going to walk thru setting up a Dockerfile for a (very) simple web application that we will turn into a docker image that can be pulled and run on any local operating system. The Dockerfile defines and builds your image to run in a container. You’ll define your parent image, create a working directory in the container, and define build commands. You can see all of the possible commands you can execute in the Dockerfile [here](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/).

__Note: We’re going to use a bare-bones TypeScript server that you can access [here](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/). Clone it to your local machine with: `git clone git@github.com:Reidweb1/TypeScriptStarter.git` or download the zip.__

Navigate into your application’s directory and run `touch Dockerfile`. Then go ahead copy the code below into your `Dockerfile`. We’ll walk through each line below.

```Dockerfile
# Docker Parent Image with Node and Typescript
FROM reidweb1/node-typescript:1.0.0

# Create Directory for the Container
WORKDIR /app

# Copy the files we need to our new Directory
ADD . /app

# Expose the port outside of the container
EXPOSE 3000

# Grab dependencies and transpile src directory to dist
RUN npm install && tsc

# Start the server
ENTRYPOINT ["node", "dist/"]
```

__Note: Your local Dockerfile should NOT have a .txt extension. Your Dockerfile should have a blank file extension (gist doesn’t allow null file extensions so this one has a .txt).__

Below is a brief explanation of each consequential line in this file.

`FROM zaherg/node-toolkit`

This line defines a different docker image as our parent image. This means that our image will be “stacked” on top of zaherg/node-toolkit. We need this so the lightweight operating system our container will ship with (Ubuntu) is preinstalled with Typescript and Node. (See the Node Toolkit repo [here](https://github.com/docker/docker-ce)).

`WORKDIR /app`

This creates a new directory in our Linux container where we’ll move all of the files that will make our app run.

`ADD . /app`

This copies all the files in our current directory to the app directory we created above.

`EXPOSE 3000`

Since we are running our app on port 3000 (see line 5 in index.ts) we need to expose the port 3000 on our container to any listeners. In this case — we’re only exposing port 3000 since we’re only serving HTTP content. Later on, we’ll map the container’s exposed port 3000 to a listener, localhost:3000, in order to run our application.

`RUN npm install && tsc`

RUN will execute given commands to set up the container. We’re first going to run npm install to pull in all the dependencies defined in our package.json. Then we’ll run tsc to transpile our Typescript code in the src directory to Javascript that will live in the dist directory.

`ENTRYPOINT ["node", "dist/"]`

This tells the container to run node dist/ once it’s up and running.

You’re bound to run into Dockerfiles that will use `CMD` instead of `ENTRYPOINT`. The difference is `CMD` is much easier to override when running a Docker image. So if you want flexibility with what your container does after it’s instantiated, using `CMD` is better. For our purposes, we want node dist/ to be the only option; so we’re using `ENTRYPOINT`.

For more info on Dockerfile commands you can checkout their docs.

## Build and Share your Image

Now that we have all the components ready to go, it’s time to build our Docker image. Make sure you’re in your application’s directory and `run docker build -t dockertsc ..` This will build a Docker image with the contents of your current directory and add a human-readable local repository name (in our case it’s dockertsc).

Now you can run docker images and you’ll see your new image (id included) right at the top.

| ![Check the image id](/images/blog/2017-12-13-intro-to-docker-with-typescript/image-command.png) |
|:--:|

You’ll also see the parent image “zaherg/node-toolkit” since it was downloaded during the build phase.

Now we can run our app locally using `docker run -p 4000:3000 dockertsc`. This command will map the container’s port 3000 (the one we exposed) to localhost:4000. You can now see your awesome HTML at localhost:4000. (You can also run curl http://localhost:4000.) Control+C stops the application.

__From the Docker Docs: You may need to use the Docker Machine IP instead of localhost. For example, http://192.168.99.100.4000/. To find the IP address, use the command docker-machine ip. The command would now look like this `docker run -p http://192.168.99.100.4000/:3000 dockertsc`.__

Just like remote code repositories - images can be hosted and cloned. Next we’re going to push our image to a remote repo so other folks can access it. If you don’t have a Docker account, sign up at cloud.docker.com. Then run docker login and enter your basic auth credentials.

The command to associate a local image with a remote repository is docker tag image username/repository:tag.

- The image part should be the local image name, in our case it’s dockertsc. 
- The username is your Docker username. 
- The repository is whatever you want to name your remote repository. 
- The tag is optional, but highly recommended. 

__Note: `tag` is a handy way for you to specify the difference between images stored in the same repo (very useful for versioning). It’s important to note that the default tag is `latest`.__

Now upload your new image to the remote with docker push image. Now the image is going to be the name (`username/repository`) of new image you just tagged (not the original image tagged `latest`). Use `docker images` to pull up all of your images to make sure you use the correct name.

## Conclusion

Now you can run the remote image with `docker run -p 4000:80 username/repository:tag`. This will do the same thing as our `run` call above, but if it doesn’t find a local image, it will pull the tagged version from the remote repository.

Now you’re up and running with Docker! 🚢 🐳 🚢