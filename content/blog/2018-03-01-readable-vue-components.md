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
import router from './router'
import 'buefy/lib/buefy.css'

Vue.use(Buefy)

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```

## Building the Components

We will be building a simple todo list app, which will have the following functionality:
1. Add items to the list
2. Remove items from the list
3. Edit items on the list
4. Check off items on the list

Let's start by adding a new folder to hold our components. Create a folder called `CheckList` in `src/components` and put the following files inside of it:

1. CheckList.vue
2. template.html
3. script.js
4. style.css

Next, we will hook up the `template`, `script`, and `style` files in the `CheckList` file:

```html
<template src='./template.html'/>
<script src='./script.js'/>
<style src='./style.css'/>
```

Now we can focus on the individual pieces that make up the `.vue` file, rather than having them all crammed in the same file!

## Building the Template

Here is what we are going to want our Checklist to look like:

<!--      Header       -->
<!--  New Item Input   -->
<!--                   -->
<!--  Checklist Items  -->

```html
<div class='container is-fluid'>
  <div class='section'>
    <div class='checklist-box'>
      <!-- Header -->
      <h1>Checklist</h1>

      <!-- New Item Input -->
      <b-field>
        <b-input
          v-model='newItem'
          expanded
          @keyup.native.enter='addItem'
        />
        <p class='control'>
          <button class='button is-primary' @click='addItem'>
            Add
          </button>
        </p>
      </b-field>

      <hr v-show='checklistItems.length > 0'/>

      <!-- Checklist Items -->
      <ChecklistItem 
        v-for='item in checklistItems' 
        :key='item.key'
        :itemTitle='item.title'
        :indexKey='item.key'
      />
    </div>
  </div>
</div>
```

## Writing the Script

```javascript
import CheckListItem from './CheckListItem/CheckListItem'

// ==================
// NAME
// ==================
const name = 'Checklist'

// ==================
// PROPS
// ==================

// ================
// DATA
// ================
const data = function () {
  return {
    checklistItems: [],
    newItem: ''
  }
}

// ================
// COMPUTED
// ================

// =================
// METHODS
// =================
const addItem = function () {
  let key = 0
  if (this.checklistItems.length !== 0) {
    key = this.checklistItems[this.checklistItems.length - 1].key + 1
  }
  this.checklistItems.push({ key, title: this.newItem })
  this.newItem = ''
}

const deleteItem = function (indexKey) {
  for (let i = 0; i < this.checklistItems.length; i++) {
    if (this.checklistItems[i].key === indexKey) {
      this.checklistItems.splice(i, 1)
      break
    }
  }
}

export default {
  name,
  components: {
    'ChecklistItem': CheckListItem
  },
  props: {},
  data,
  computed: {},
  methods: {
    addItem,
    deleteItem
  }
}
```

## Writing the CSS

```css
.checklist-box {
    margin-left: auto;
    margin-right: auto;
    width: 85%;
    border-radius: 5px;
    border: 1px solid #E7EAEC;
    box-shadow: 0px 0px 4px rgba(134, 134, 134, 0.21);
    padding: 15px 15px 15px 15px;
}
```

## Lets Compare

This is what the `CheckList.vue` file would look like if you
followed the format specified in the official Vue documentation.

```html
<template>
    <div class='container is-fluid'>
    <div class='section'>
        <div class='checklist-box'>
        <!-- Header -->
        <h1>Checklist</h1>

        <!-- New Item Input -->
        <b-field>
            <b-input
                v-model='newItem'
                expanded
                @keyup.native.enter='addItem'
            />
            <p class='control'>
            <button class='button is-primary' @click='addItem'>
                Add
            </button>
            </p>
        </b-field>

        <hr v-show='checklistItems.length > 0'/>

        <!-- Checklist Items -->
        <ChecklistItem 
            v-for='item in checklistItems' 
            :key='item.key'
            :itemTitle='item.title'
            :indexKey='item.key'
        />
        </div>
    </div>
    </div>
</template>

<script>
import CheckListItem from '@/components/CheckList/CheckListItem/CheckListItem'
export default {
    name: 'CheckList',
    components: {
        'CheckListItem': CheckListItem
    },
    props: {},
    data () {
        return {
            checklistItems: [],
            newItem: ''
        }
    },
    computed: {},
    methods: {
        addItem: function () {
            let key = 0
            if (this.checklistItems.length !== 0) {
                key = this.checklistItems[this.checklistItems.length - 1].key + 1
            }
            this.checklistItems.push({ key, title: this.newItem })
            this.newItem = ''
        },
        deleteItem: function (indexKey) {
            for (let i = 0; i < this.checklistItems.length; i++) {
                if (this.checklistItems[i].key === indexKey) {
                    this.checklistItems.splice(i, 1)
                    break
                }
            }
        }
    }
}
</script>

<style>
.checklist-box {
    margin-left: auto;
    margin-right: auto;
    width: 85%;
    border-radius: 5px;
    border: 1px solid #E7EAEC;
    box-shadow: 0px 0px 4px rgba(134, 134, 134, 0.21);
    padding: 15px 15px 15px 15px;
}
</style>
```

While it is _manageable_, it is certainly becoming quite a large file. This is a fairly simple component as well; so trying to build something more involved simply will not scale well. Also notice that none of the functions have any comments or documentation. I find that the structure of the <script/> tag makes the code difficult to read to begin with, as it forces you to compress everything into a small space (both vertically and horizontally). Separating the JavaScript into its own file allows you to make everything more readable, and gives you more space to work.

Splitting the CSS file out of the `.vue` file can also be very helpful, especially if you use classes that are repeated in many different component templates.