---
title: "React-Native Typescript Boilerplate"
sub_title: "Build cross-platform mobile apps faster."
tags: ["React Native", "TypeScript", "Mobile"]
date: 2018-09-14T09:44:38-07:00
publishdate: 2018-09-14T09:44:38-07:00
hero_image: /images/blog/2018-09-14-react-native-boilerplate/hero.png
author:
  name: Reid Weber
  url: https://medium.com/@reidweb1
  mail: reid@meshstudio.io
  avatar: https://avatars2.githubusercontent.com/u/8824846?s=460&v=4
---

React Native has established itself as an incredibly powerful tool for building cross-platform mobile applications. We set out to try and create a clone-able repository so developers can jump right into building features/UI instead of spending hours configuring their environment.


### Getting Started


If you want to jump right into developing your own cross-platform mobile application, get started with the README [here](https://github.com/meshhq/react-native-boilerplate).

If you want more insight into the tooling/frameworks used in this project - continue reading.

First go ahead and clone the repository so you can follow along in the codebase.

```
git clone git@github.com:meshhq/react-native-boilerplate.git
```


## Tooling


> [`react-native-cli`](https://facebook.github.io/react-native/docs/getting-started.html)

There are currently two ways to create a new React Native application;  `react-native-cli` and `create-react-native-app` (`CRNA`). We chose the former because it provides more flexibility when it comes to configuring settings in Xcode and Android Studio. `react-native-cli` also makes it much easier to link third-party libraries to underlying native libraries. 

There is, however, a downside; more flexibility in local configuration can lead to build and runtime errors in Android Studio and Xcode (this is especially apparent in Android Studio with the way it is constantly syncing Gradle files).

> `Project Directories`

You’ll see the `android` and `ios` directories at the root of the project. Both of these can be opened and manipulated in their respective IDE’s.

Note: Never edit Gradle files outside of Android Studio. It’s very easy to create build issues without taking advantage of the sync process embedded in Google’s IDE.


> [`TypeScript`](https://www.typescriptlang.org/)

Having strongly typed class and components makes it much easier to understand and interpret the original author’s code. This is especially useful when dealing with React component’s `state` and `props`. The only drawback with TypeScript is setting up the environment (which is already taken care of in this project).


> [`react-native-navigation`](https://github.com/wix/react-native-navigation)

An open-source library maintained by Wix that provides developers with access to built-in navigation animations you’d expect from a native application. This project comes prebuilt with a `TabBarNavigator` and a couple of screens to illustrate best practices on how to set up your navigation stack. 

Note: If you’re interested in building a single screen application. See the docs [here](https://wix.github.io/react-native-navigation/#/top-level-api).


> [`jest`](https://jestjs.io/)

Jest is a great unit testing suite that ships with React Native. It provides a simple, flexible syntax that is powerful and human-readable. Jest also runs all tests in parallel - speeding up the test process. In this project, run the test suite with 

```
yarn jest
```


> [`detox`](https://github.com/wix/detox)

Another great open-source resource from Wix that allows end-to-end UI testing. Allows you to tag all of your components and manipulate them to simulate user inputs. This project has a single UI test in `./e2e/HomeScreen.spec.js` to illustrate the correct way to label and manipulate components.


> [`axios`](https://github.com/axios/axios)

A Promise based HTTP client with TypeScript and React Native support. Allows a simple interface for clean HTTP requests and also allows automatic transforms for JSON data. Clean, simple, examples of how axios can be used to connect your application to remote resources can be found in `UserService.ts`.


> [`nock`](https://github.com/nock/nock)

A mocking library that allows you to simulate HTTP requests/responses in the test suite without hitting a live endpoint. This is helpful if you’re building a client application before its server is ready to hit. You can mock the expected responses to make sure your application is ready to send/receive data when the servers are up. Mocking examples can be found in `__tests__/models/UserService.ts`.


> [`Realm`](https://realm.io/)

A simple, yet powerful solution to local storage in React Native. Realm makes it easy to define model schemas and provides a clean, synchronous interface to manage your local store. An example of a model definition can be found in `./src/models/User.ts`. An example of how a model’s functionality and repository can be exposed can be found in the `store` directory.


> [`Watchman`](https://facebook.github.io/watchman/docs/install.html)

Also ships with React Native, it tracks changes in your file system to enable the hot reload. To see this in action - run the project on a device and perform a ‘shake-gesture’. The React Native developer menu will pop up and you can select ‘Reload’ to quickly see any new changes you’ve made since the last launch.

Now you can move on to building awesome cross-platform features and UI - no need to worry about all the boring configuration stuff.