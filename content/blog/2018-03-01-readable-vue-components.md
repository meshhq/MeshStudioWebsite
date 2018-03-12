---
title: "Writing Better Components in Vue.js"
sub_title: "Scalable, readable, and maintainable"
tags: ["JavaScript", "WebApp", "Vue"]
date: 2018-03-01T12:56:33-08:00
publishdate: 2018-03-01T12:56:33-08:00
hero_image: "/images/blog/2018-03-01-readable-vue-components/hero.png"
author:
  name: "John Daly"
  url: "http://johnmcdaly.com/"
  mail: "john@meshstudio.io"
  avatar: "https://avatars2.githubusercontent.com/u/1719443?s=460&v=4"
---

## Pros and Cons of Vue.js

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

### CheckList Component

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

#### Building the Template

Here is what we are going to want our Checklist to look like:

```
<--      Header       -->
<--  New Item Input   -->
<--                   -->
<--  Checklist Items  -->
```

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

We will be adding the `ChecklistItem` component later, so don't worry about it for now.

#### Writing the Script

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

/**
 * Creates a new item from the form values, and pushes 
 * it into the checklistItems list
 */
const addItem = function () {
  let key = 0
  if (this.checklistItems.length !== 0) {
    key = this.checklistItems[this.checklistItems.length - 1].key + 1
  }
  this.checklistItems.push({ key, title: this.newItem })
  this.newItem = ''
}

/**
 * Removes the item with the given indexKey from the checklistItems list
 * @param indexKey Number the key of the item to remove
 */
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

#### Writing the CSS

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

### CheckListItem Component

The CheckListItem component will be the individual listings that appear within the CheckList. We will start by creating a new folder `CheckListItem` within our existing `CheckList` folder. As before, we will add the following 4 files:

1. CheckListItem.vue
2. template.html
3. script.js
4. style.css

Matching the way we made the `CheckList` component, the `CheckListItem.vue` file will look like this:

```html
<template src='./template.html'/>
<script src='./script.js'/>
<style src='./style.css'/>
```

#### Building the Template

For our template, we want an item to look like this:
```
+----------------------------------+
|[ ] [   <Item Description>   ] [X]|
+----------------------------------+
 (A)            (B)             (C)
```

Here is a breakdown of what makes up this component:

- A) Checkbox to mark an item as being finished
- B) Text field that includes a description of the item, it will be possible for user's to edit this field
- C) Button to remove the item from the CheckList


```html
<b-field>
  <!-- Item Finished Checkbox -->
  <b-checkbox v-model='itemChecked'/>

  <!-- Item Description Text -->
  <span
    class='item-static'
    :class="{'item-checked': itemChecked}"
    v-show='!editingItem'
    @dblclick='editItem'
  >
    {{itemTitleDone}}
  </span>

  <!-- Edit Item Description Input -->
  <b-input 
    v-model='itemTitleEdit'
    v-show='editingItem'
    :disabled='itemChecked'
    @blur="doneEdit"
    @keyup.native.enter="doneEdit"
    @keyup.native.esc="cancelEdit"
    expanded/>

  <!-- Delete Item Button -->
  <p class='control'>
    <button class='button is-danger' @click='deleteItem'>
      <b-icon icon='close'/>
    </button>
  </p>
</b-field>
```

#### Writing the Script

```javascript
// ==================
// NAME
// ==================
const name = 'ChecklistItem'

// ==================
// PROPS
// ==================
const props = {
  indexKey: { type: Number, required: true },
  itemTitle: { type: String, required: true }
}

// ================
// DATA
// ================
const data = function () {
  return {
    itemChecked: false,
    editingItem: false,
    itemTitleDone: this.itemTitle,
    itemTitleEdit: this.itemTitle
  }
}

// ================
// COMPUTED
// ================

// =================
// METHODS
// =================

/**
 * Sets the value of 'editingItem' to true if the item
 * hasn't already been checked off as completed
 */
const editItem = function () {
  if (!this.itemChecked) {
    this.editingItem = true
  }
}

/**
 * Reverts the item back to its previous description
 * and hides the editing input field
 */
const cancelEdit = function () {
  this.editingItem = false
  this.itemTitleEdit = this.itemTitleDone
}

/**
 * Sets the description of the item to the value of the
 * input field that is used to edit the description. If the user
 * has entered a blank value, the item is deleted
 */
const doneEdit = function () {
  this.itemTitleDone = this.itemTitleEdit
  this.editingItem = false
  if (this.itemTitleDone === '') {
    this.deleteItem()
  }
}

/**
 * Deletes the item from the parent CheckList component
 */
const deleteItem = function () {
  this.$parent.deleteItem(this.indexKey)
}

export default {
  name,
  props,
  data,
  computed: {},
  methods: {
    editItem,
    cancelEdit,
    doneEdit,
    deleteItem
  }
}

```

#### Writing the CSS

```css
.item-static {
  width: 100%;
  width: -moz-available;
  width: -webkit-fill-available;
  width: fill-available;
  display: flex;
  align-items: center;
  border: 1px #dbdbdb solid;
  padding-left: 10px;
}

.item-checked {
  text-decoration: line-through;
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

and here is the `CheckListItem.vue` file with the same standard format:

```html
<template>
  <b-field>
    <b-checkbox v-model='itemChecked'/>
    <span
      class='item-static'
      :class="{'item-checked': itemChecked}"
      v-show='!editingItem'
      @dblclick='editItem'
    >
      {{itemTitleDone}}
    </span>
    <b-input 
      v-model='itemTitleEdit'
      v-show='editingItem'
      :disabled='itemChecked'
      @blur="doneEdit"
      @keyup.native.enter="doneEdit"
      @keyup.native.esc="cancelEdit"
      expanded
    />
    <p class='control'>
      <button class='button is-danger' @click='deleteItem'>
      <b-icon icon='close'/>
      </button>
    </p>
  </b-field>
</template>

<script>
export default {
  name: 'CheckListItem',
  props: {
    indexKey: { type: Number, required: true },
    itemTitle: { type: String, required: true}
  },
  data () {
    return {
      itemChecked: false,
      editingItem: false,
      itemTitleDone: this.itemTitle,
      itemTitleEdit: this.itemTitle
    }
  },
  computed: {},
  methods: {
    editItem: function () {
      if (!this.itemChecked) {
        this.editingItem = true
      }
    },
    cancelEdit: function () {
      this.editingItem = false
      this.itemTitleEdit = this.itemTitleDone
    },
    doneEdit: function () {
      this.itemTitleDone = this.itemTitleEdit
      this.editingItem = false
      if (this.itemTitleDone === '') {
        this.deleteItem()
      }
    },
    deleteItem: function () {
      this.$parent.deleteItem(this.indexKey)
    }
  }
}
</script>

<style>
.item-static {
  width: 100%;
  width: -moz-available;
  width: -webkit-fill-available;
  width: fill-available;
  display: flex;
  align-items: center;
  border: 1px #dbdbdb solid;
  padding-left: 10px;
}

.item-checked {
  text-decoration: line-through;
}
</style>
```

While both of these examples are _manageable_, they are certainly becoming quite long files. These are fairly simple components as well; so trying to build something more involved simply will not scale well. Also notice that none of the functions have any comments or documentation. I find that the structure of the `<script/>` tag makes the code difficult to read to begin with, as it forces you to compress everything into a small space. Notice how the methods begin with three levels of indentation; this is horizontal space that would be nice to have back, especially for writing comments or longer statements. Separating the JavaScript into its own file allows you to make everything more readable, and gives you more space to work.

Splitting the CSS file out of the `.vue` file can also be very helpful, especially if you use classes that are repeated in many different component templates.

## Wrapping Up

Vue is a great framework to quickly get an app up and running, and has great developer support as well. I think it is a worthy alternative to other frameworks like React and Angular. My initial impression of Vue is that it would be a good choice for a weekend project or small prototype, and that it wouldn't scale well because of how `.vue` files are recommended to be formatted. By splitting the `.vue` files into smaller pieces, it seems to me that Vue is a much more viable option for larger projects. If you have been on the fence about starting a project in Vue, I highly recommend giving it a shot.

How do you structure your Vue projects? Do you use the standard `.vue` format? How have you tackled issues of scalability, maintainability, and readability. Feel free to reach out and let us know!