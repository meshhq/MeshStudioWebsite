---
title: Docker Compose and TypeScript
sub_title: Setting up Docker Compose for your TypeScript application.
tags: []
date: 2018-02-13T13:48:41-08:00
publishdate: 2018-02-13T13:48:41-08:00
hero_image: /images/blog/2017-12-13-intro-to-docker-with-typescript/docker-typescript-hero.png
author:
  name: Reid Weber
  url: https://medium.com/@reidweber_34407
  mail: reid@meshstudio.io
  avatar: "https://avatars2.githubusercontent.com/u/8824846?s=460&v=4"
---

# Docker Compose and Typescript

These days, it’s easy for web applications to quickly balloon with dependencies and third party libraries. Managing your applications within a team can be a real headache when you start running into versioning issues, db configuration issues, local machine compatibility issues, etc... Docker Compose can help a lot with these issues — along with giving you a much cleaner way to get your app up and running.

__If you want an intro to using Docker with Node and Typescript go [here](https://blog.meshstudio.io/intro-to-docker-with-typescript-21b89d8f8470).__

## What is Docker Compose?

Docker Compose allows you to package multiple Docker images into a single application. Each image is still going to run in it’s own container but it allows you to get complex applications up and running with a single command.

This is achieved with the `docker-compose.yml` file (more info on [YAML](http://www.yaml.org/spec/1.2/spec.html)). The Docker Compose file defines all of the services (individual Docker images) provided in your application. In this post, we’re going to have a simple Typescript server that will ship with a [PostgreSQL](https://www.postgresql.org/docs/9.1/static/tutorial-advanced-intro.html) image that will serve as the DB.

## The docker-compose.yml file

We’ve built a starter app with Node, Express, and Typescript. You can go ahead and clone it here (note that this project already has a `Dockerfile` set up).

Create the `docker-compose.yml` file by running `touch docker-compose.yml`. Open it up and copy the following:

```YAML
version: "3"
services:
  web:
    build: .
    command: gulp
    ports:
      - "8080:3000"
    depends_on:
      - postgres
  postgres:
    image: postgres:9.6.2-alpine
```

The most important function of the `docker-compose.yml` is to define what the compose application is going to look like. Think of it like a blueprint not only for each container, but for a complete application as well. The two main actions we’ll take with Compose file in this tutorial are:

- Define the different services (containers).
- Link them to each other (using `depends on`).

Below we’ll go line by line and explain the reason for each key-value pairing.

`version: "3"`

This line defines which version of Compose we want to use. "3" is the most recent release so we’ll be using that.

`services:`

This line is the key for the “array” that will hold all of the different docker images we’re including in the final application. Each of these services will end up being its own running container inside our application. We’re using the web key for the container that will be running our server. After the web: key you can add any amount of other services you want. In this example we only have one other service — postgres.

`web:`

This defines all the details for our main web application (in this case it’s just a Node server). In here we’ll define the build directory, an entry point run command, the ports we’re mapping from the container to the local machine, the other containers we’re listing as dependencies, and any ENV variables we want to set. We’ll go into greater detail for each of these below.

`build: .`

The build key tells docker-compose that it needs to build the image that will be used for the web service. The path supplied to the build parameter tells docker-compose where it should look for a Dockerfile from which to build the image. In this case, we are telling docker-compose that our Dockerfile is in the current directory.

`command: gulp`

This will override the default CMD defined in the image’s Dockerfile. For our app we’re going to run gulp since we’ve built our gulpfile to handle the transpile functionality as well as get the server running.

`ports:`

This is the mapping from the container’s internal port to the external port the consumer will be listening on. In our case, the server is using 8080 and the container is exposing 3000.

`depends_on:`

This is marking our posrgres service as a dependency for our web container.
postgres:

Here we’re defining our second service (container). You can name the service key whatever you want. In this case just make sure this name matches the name you’re using under depends_on.

`image:`

This is the image our service will use to create its container.

__Note: One missing key here is volumes. Each service can define volumes to persist data within the compose container. Normally, for something like `postgres` we’d need to define a `volume` to handle persistence. For this example, we’re not going to define a volume for the DB; we’ll use the default configuration. The default configuration just uses the [CoW](https://en.wikipedia.org/wiki/Copy-on-write) filesystem that ships with the container.__

Now that we have our Compose file set up we can run the following: `docker-compose up`. Now our application is up and running!

| ![See if Docker is running.](/images/blog/2017-12-29-docker-compose-and-typescript/docker-compose-running.png) |
|:--:|
| This will pull all the images we need and give us the container ID our application is running in (2f7b7a57d7c0 in this case). |

## Final thoughts.

With Compose we can bundle together complex applications without worrying about versioning or configurations on the machine it’s running on. On top of that we can get applications with multiple Docker images up and running with a single command. It also gives us a way to package multiple containers together within one, very manageable, bundle. 🐙💾 💾 💾 🐙