---
title: Setting Up CircleCI
sub_title: How to setup a CirclCI Continuous Integration Environment in Github
tags: ["Continuous Integration"]
date: 2018-01-05
publishdate: 2018-01-05
hero_image: /images/blog/circle.png
author:
  name: Kevin Coleman
  url: https://twitter.com/kfccoleman1
  mail: kevin@meshstudio.io
  avatar: https://avatars2.githubusercontent.com/u/1423931?s=460&v=4
---

# Contiuons Integration with Node.js and CircleCI

Software developers tend to come up with complicated names for simple topics. Examples of this include Dependency Injection, Orthogonality, or [think of something]. While these terms sound impressive, they often produce an adverse side effect; novice developers assume the subjects they are describing are complicated and unapproachable.

https://mobile.twitter.com/tenderlove/status/748579020703313920

The above proved to be true for me when I first learned about Continuous Integration (CI). I thought It sounded like a complex topic and one that I would never be able to understand let alone master. In reality however, CI is very approachable and easy to understand. It is also straightforward to integrate into any development process, especially in this day and age when there are many fantastic CI hosting providers available from which to choose. 

In this post, I'll explain what CI is and demonstrate how you can add a CI pipeline to an existing project. I'll also demonstrate how we can integrate that pipeline with Github so that the process automatically runs on every developer commit. Lastly, I'll show we can add CI reporting notifications to everyone's favorite chat app, Slack. 

## What is CI
	
CI is the process of building and testing a code base whenever an individual developer makes a new. This process should be automated, meaning it happens whenever a developer pushes new code. 

At Mesh Studio, we are huge proponents of CI. It is a potent tool that helps development teams ensure the stability of their code base at all times. When we engage with a new customer, we will often instrument a CI pipeline before we start writing any feature code. 

## Getting Started

For this tutorial, we are going to use a sample project `circle-sample`. Clone the project onto your local machine and CD into the directory.

```
$ git clone git@circle-sample && cd circle-sample
```

Our CI pipeline is going to consist of the following three steps:

1. Install dependecies
2. Build the project
3. Execute the test script

Our sample project provides simple npm commands for each of these steps. These command are `npm install`, `npm build` and `npm test`. We will use all three in our CircleCI build configuation.

### Setting up CircleCI 

Our CI provider of choice at Mesh is [Circle CI](https://circleci.com). To run builds on CircleCI, we need to create a new directory in the root of our project called `.circleci`.

```
$ mkdir .circleci
```

Next, we need to create a file called `circle.yml` inside of the new `.circleci` directory. 

```
$ touch .circleci/circle.yml
```

The `circle.yml file will contain the build instructions that CircleCI follows for your build. For this tutorial, we will use the circle.yml file below. Copy and paste the following code into your circle.yml file. 

```version: 2
jobs:
  build:
    docker:
      - image: circleci/node:7.10

    working_directory: ~/repo

    steps:
      - checkout

      - run: npm install

      - run: npm run build
        
      - run: npm run test
```

#### circle.yml 

Let's dive into each section of the `circle.yml` file and examine what each entry means. 

##### Jobs

The `jobs` section of the circle.yml defines a task or set of tasks to be run for the CI process. CircleCI allows you to configure multiple Jobs for more complex CI processes, but for our purposes, a single `job` task is all we need. 

##### Build

The `build` section defines the name for the `job`. So in this case, the name of the `job` is simply `build`.

##### Docker

The `docker` section defines an [executor](https://circleci.com/docs/2.0/executor-types/) for the build process. An executor is a place where build steps will occur. By specifying `docker`, we are telling Circle that we want our build steps to take place inside of a Docker container. For comparison sake, if we were building an `iOS` application instead of a `node.js` application, we could use the `macos` executor. 

##### Image

When we use the `docker` executor, we must define the `image` that we want to use for the `executor`. Circle offers many images that we can use for a variety of runtimes. In this case, we have decided to use a Circle image that supports node.js v7.10.

##### Working Directory 

##### Steps 

The `steps` section defines a list of steps that need to be executed to complete the build process. Each of these steps must pass without failure; otherwise, the CI process will fail. We describe the steps, and their functionality below: 

* `checkout` - A circle CI built in command which is responsible for checking out the projects source code into the Job's `working_directory`
* `run: npm install` - Installs all project dependencies listed in the `package.json` file. 
* `run: npm build` - Compiles and builds the application
* `run: npm test` - Executes the automated test suite. 

### Running the build locally

One of the fantastic features of CircleCI is that they distribute a command line tool which provides for running build locally on a developers machine. Now that we have our `circle.yml` file configured, we can run the build locally and ensure that the build process passes. 

To do so, we first need to install the Circle command line tool. We can do this with the following command:

```
$ curl -o /usr/local/bin/circleci https://circle-downloads.s3.amazonaws.com/releases/build_agent_wrapper/circleci && chmod +x /usr/local/bin/circleci
```

To validate your installation, type `circleci -h`. You should see the following output.

```
$ circleci -h                                                                                                                                                                                                                                                                                                                     

The CLI tool to be used in CircleCI.

Usage:
  circleci [flags]
  circleci [command]

Available Commands:
  build       run a full build locally
  config      validate and update configuration files
  help        Help about any command
  step        execute steps
  tests       collect and split files with tests
  version     output version info

Flags:
  -c, --config string   config file (default is .circleci/config.yml)
  -h, --help            help for circleci
      --taskId string   TaskID
      --verbose         emit verbose logging output

Use "circleci [command] --help" for more information about a command.
```

Once the cli is installed, we can kick off a local build with the following command.

```
$ circleci build
```

Your build process should begin executing and pass succesfully! Congratulations, you have configured your build with CircleCI! 

## Integrating Circle CI and Github

Now that we have our CircleCI build configured and passing, the next step is to take our build process and make it continuous. We do so by telling Circle to run our CI process anytime a developer opens a pull request or makes a commit to an open PR. We are also going to protect the master branch of our repository to prevent code from being merged unless it has passed CI. We can set this up with the following steps:

1. Navigate to your project's repository and click on `Settings.`
2. Click on `Branches` in the left-hand navigation menu. 
3. Under `Protected Branches` click on the `Chose a branch` drop-down and select `master`.
4. Click the radio button next to `Protect this branch`.
5. Click the radio button next to `Require status checks to pass before merging`.
6. Click the radio button next to `Require branches to be up to date before merging`.

At this point, you should see the following dialog box. This is expected. Leave this browser window open for the time being. For the next steps, we need to head over to Circle. 

## Adding project to CircleCI

Head over to www.circleci.com and login with your Github account. Once logged in, you should see the circle CI dashboard. We need to tell Circle that it should start building our Github project. We can do this with the following step:

2. Click on `Projects` in the left navigation bar. 
3. Click the `Add Project` button in the upper right-hand corner. 
4. Find your repository and click the `Setup Button` on the far right-hand side of the screen.
5. Scroll past the setup information and click the `Start Building` button. 

This will kick off your first build process with CircleCI. Note, the build process will fail as we haven't yet pushed up our circle configuration files to our repo. 

## Completeing the Github Setup

Next, lets go back to our open Github browser window and refresh the screen. We should now see the CircleCI option. 

## Pushing our configuration

At this point, we can push up our CircleCI configuration. Push the branch. 

After pushing navigate to Github and open up a PR with our branch. If you navigate to the PR screen, you will be able to see the CircleCI build process information directly from the pull request window. 

Once the build process process, everything will be green and you will be able to merge your pull request to master. 

## Adding CI notifications to Slack

Now that we have our continuous integration pipeline setup, we need to let other developers on our team know when build pass. To do so, we can integrate slack with CircleCi. 

First we need to enable CircleCI from within Slack. To do so visit the following URL 

https://gawkbox.slack.com/apps/new/A0F7VRE7N-circleci

1. Select the channel that you would like notifications to be sent to. 
2. Copy the link displayed under step 2. 
3. Navigate back to circle CI dashboard and click on your project.
4. Click on the `Settings` icon in the top right hand corner. 
5. Under the `Notifications Section` in the left hand naviagtion, click on `Chat Notifications`. 
6. Enter the copied link into the bar. 

And that is it! Your CircleCI build pipeline is now fully integrated with Slack

## Conclusion
Continuous integrations is a fantastic tool that helps developers build reliable code bases. We would encourage everyone to add CI and branch protection to all of their projects. 

If you ever need help setting up your CI build process, don't hesitate to reach out!



