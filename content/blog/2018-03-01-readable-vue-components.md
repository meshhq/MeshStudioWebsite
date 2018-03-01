---
title: "Readable Components in Vue.js"
sub_title: ""
tags: ["JavaScript", "WebApp", "Vue"]
date: 2018-03-01T12:56:33-08:00
publishdate: 2018-03-01T12:56:33-08:00
hero_image: ""
author:
  name: "John Daly"
  url: "http://johnmcdaly.com/"
  mail: "john@meshstudio.io"
  avatar: "https://avatars2.githubusercontent.com/u/1719443?s=460&v=4"
---

## Building easy to maintain Vue Components!

When starting a new project, you want to get things up and running as quickly as possible. While modern JavaScript advancements have made things a lot easier on developers, they have also increased the complexity of getting things going. Every developer who has worked with Webpack knows what I am talking about; Webpack is a great example of a very powerful tool that has an incredibly steep learning curve, even for doing basic things. Fortunately there is a solution for those who want to go fast: `Vue` and `vue-cli`.

I don't think I have come across a solution that so easily gets you up and on your way to developing a modern JavaScript web application. The `vue-cli` frees you from the tedious initial Webpack configuration, and allows you to start writing application code right away. While this is great, I have found there to be some shortcomings to Vue itself; mainly that Vue code can be very messy and cluttered. Here is what the standard format for a .vue file looks like:

```html
<template>
    <!-- HTML goes here -->
</template>

<script>
    // JavaScript goes here
</script>

<style>
    /* CSS goes Here */
</style>
```

The HTML, JavaScript code, and CSS are all located in one single file. While this is okay for small components, and you could argue that it promotes keeping things simple and breaking big components into many smaller components, I find that it can easily result in monolithic files which are hard to read and difficult for others to maintain.

The official Vue documentation promotes this style as the way to write Vue code. Honestly, I think that this would prove to be a potential deal breaker for me if I was deciding between using React or Vue for a larger web app. Fortunately though, there is a way to separate the three pieces of the .vue file into their own files, leading to code that is easier to read and maintain.

## Lets Build a Vue App

To see how easy `vue-cli` makes things for us, lets start from scratch:

1. Download the `vue-cli`
```
npm install -g @vue/cli
```

2. Create your project with `vue-cli`
```
vue init webpack <project-name-here>
```
This command will lead you through a step-by-step process to configure your project. Here is what I selected for this project:
```
? Project name readable-vue-components
? Project description A Vue.js project
? Author John Daly <john@meshstudio.io>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests Yes
? Pick a test runner jest
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm
```

Congratulations, you have successfully set up your project! One last thing before we start building components, we are going to install Buefy, a great UI package for Vue:

3. Install Buefy
```
npm install --save-dev buefy
```

4. Adding Buefy to the Project

Go into `src/main.js` and add the following imports:

```javascript
import Buefy from 'buefy'
import 'buefy/lib/buefy.css'
```

Next configure Vue to use Buefy:

```javascript
Vue.use(Buefy)
```

Your `main.js` file should now look like this:

```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import Buefy from 'buefy'
import App from './App'
import 'buefy/lib/buefy.css'

Vue.use(Buefy)

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
})
```

## Building the Components