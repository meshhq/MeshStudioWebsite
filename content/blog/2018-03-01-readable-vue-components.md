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

When starting a new project, you want to get things up and running as quickly as possible. While modern JavaScript advancements have made things a lot easier on developers, they have also increased the complexity of getting things going. Every developer who has worked with Webpack knows what I am talking about; Webpack is a great example of a very powerful tool that has an incredibly steep learning curve, even for doing basic things. Fortunately there is a solution for those who want to go fast: [Vue](https://vuejs.org) and [vue-cli](https://github.com/vuejs/vue-cli).

I don't think I have come across a solution that so easily gets you up and on your way to developing a modern JavaScript web application. The `vue-cli` frees you from the tedious initial Webpack configuration, and allows you to start writing application code right away. While this is great, I have found there to be some shortcomings to Vue itself; mainly that Vue code can be very messy and cluttered. Here is what the standard format for a `.vue` file looks like:

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

The HTML, JavaScript, and CSS are all located in one single file. While this is okay for small components, and you could argue that it promotes keeping things simple and breaking big components into many smaller components, I find that it can easily result in monolithic files which are hard to read and difficult for others to maintain.

I also find that this style is the most commonly used way to write Vue code, being used in official examples and in most Vue projects I have come across. Honestly, this would prove to be a potential deal breaker for me if I was deciding between using React or Vue for a larger web app. Fortunately though, there is a way to separate the three pieces of the .vue file into their own files, leading to code that is easier to read and maintain.

## Lets Build a Vue App

To see how easy `vue-cli` makes things for us, lets start from scratch:

### 1. Download the `vue-cli`
```
npm install -g @vue/cli
```

### 2. Create your project with `vue-cli`
```
vue init webpack <project-name-here>
```
This command will lead you through a step-by-step process to configure your project. Here is what I selected for this project:
```
? Project name readable-vue-components
? Project description A Vue.js project
? Author John Daly <my email here>
? Vue build standalone
? Install vue-router? Yes
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Set up unit tests Yes
? Pick a test runner jest
? Setup e2e tests with Nightwatch? No
? Should we run `npm install` for you after the project has been created? (recommended) npm
```

Congratulations, you have successfully set up your project! One last thing before we start building components, we are going to install [Buefy](https://buefy.github.io/), a great UI package for Vue:

### 3. Install Buefy
```
npm install --save-dev buefy
```

### 4. Adding Buefy to the Project

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

### 5. Update the Router

Go into `src/router/index.js` and update it to look as follows:

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import CheckList from '@/components/Checklist/Checklist'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Checklist',
      component: Checklist
    }
  ]
})
```

We are now ready to start building the `Checklist` and `ChecklistItem` components!

## Building the Components

We will be building a simple todo list app, which will have the following functionality:

1. Add items to the list
2. Remove items from the list
3. Edit items on the list
4. Check off items on the list

### Checklist Component

Let's start by adding a new folder to hold our components. Create a folder called `Checklist` in `src/components` and put the following files inside of it:

1. Checklist.vue
2. template.html
3. script.js
4. style.css

Next, we will hook up the `template`, `script`, and `style` files in the `Checklist` file:

```html
<template src='./template.html'/>
<script src='./script.js'/>
<style src='./style.css'/>
```

Now we can focus on the individual pieces that make up the `.vue` file, rather than having them all crammed in the same file!

#### Building the Template
Lets open up our `src/components/Checklist/template.html` file and get started.

Here is what we are going to want our Checklist to look like:

```
<--      Header       -->
<--  New Item Input   -->
<--                   -->
<--  Checklist Items  -->
```

We can quickly tackle these piece by piece:

##### Header

This will be simple, just a standard `<h1>` title:

```html
<h1>Checklist</h1>
```

##### New Item Input

Next we want a text input, with a button to confirm the value:

```html
<b-field>
  <b-input
    v-model='newItem'
    expanded
    @keyup.native.enter='addItem'
  />
  <p class='control'>
    <button
      class='button is-primary'
      @click='addItem'
    >
      Add
    </button>
  </p>
</b-field>

<!-- We want a divider to be rendered if we have any items in the Checklist -->
<hr v-show='checklistItems.length > 0'/>
```

##### Checklist Items

Finally, we want the Checklist to be filled with ChecklistItem components.
We will be building this component later, but we will add this here now.

```html
<ChecklistItem 
  v-for='item in checklistItems' 
  :key='item.key'
  :itemTitle='item.title'
  :indexKey='item.key'
/>
```

##### Finished Template

To wrap up this template, we will put each of those pieces in the following set of divs:

```html
<div class='container is-fluid'>
  <div class='section'>
    <div class='checklist-box'>
      <!-- Header -->
      <!-- New Item Input -->
      <!-- Checklist Items -->
    </div>
  </div>
</div>
```

Your template file should now look like this:

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
          <button
            class='button is-primary'
            @click='addItem'
          >
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

#### Writing the Script
Next, we will open up our JavaScript file at `src/components/Checklist/script.js`.

##### Name
The name of the component will be `Checklist`:

```javascript
const name = 'Checklist'
```

##### Components
The `Checklist` component is going to render many `ChecklistItem` components as new items are added.
We need to import the `ChecklistItem` component and register it as a component of `Checklist`:

```javascript
// Put this at top of file
import ChecklistItem from './ChecklistItem/ChecklistItem'

const components = {
  ChecklistItem
}
```

##### Props
We won't need any props for this component

##### Data
According to our template, we will only need two properties in our data object:
- The description of a new item to add (newItem)
- A list, holding properties of the ChecklistItem components that will be in the Checklist

```javascript
const data = function () {
  return {
    checklistItems: [],
    newItem: ''
  }
}
```

##### Methods
The methods we will need for this component are simple add/deleteItem operations

###### addItem
addItem will create a new object, which holds information that will be used by the `ChecklistItem`
component, and push it into the `checklistItems` list.

```javascript
const addItem = function () {
  let key = 0
  const numItems = this.checklistItems.length
  if (numItems !== 0) {
    // In an effort to keep the key of an item unique in the 
    // set we will set the new item's key to be equal to the 
    // latest item's key, plus 1
    key = this.checklistItems[numItems - 1].key + 1
  }

  // Add the newItem information to the checklistItems list
  this.checklistItems.push({ key, title: this.newItem })

  // Reset the newItem value
  this.newItem = ''
}
```

###### deleteItem
deleteItem will take a `key`, and will remove the entry from `checklistItems` that has that key.

```javascript
const deleteItem = function (indexKey) {
  // Loop through the checklistItems list
  for (let i = 0; i < this.checklistItems.length; i++) {
    // Check the current element to see if we have a key match
    if (this.checklistItems[i].key === indexKey) {
      // Splice the matching element out of the
      // list and break from the loop
      this.checklistItems.splice(i, 1)
      break
    }
  }
}
```

##### Exporting the script
With the script logic written, we need to export it in the same way you export in a standard Vue file:

```javascript
export default {
  name,
  components,
  props: {},
  data,
  computed: {},
  methods: {
    addItem,
    deleteItem
  }
}
```

##### Finishing the Script
Here is what the finished script file should look like:

```javascript
import ChecklistItem from './ChecklistItem/ChecklistItem'

// ==================
// NAME
// ==================
const name = 'Checklist'

// ==================
// COMPONENTS
// ==================
const components = {
  ChecklistItem
}

// ==================
// PROPS
// ==================
const props = {}

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
const computed = {}

// =================
// METHODS
// =================

/**
 * Creates a new item from the value of newItem
 * and pushes it into the checklistItems list
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
  components,
  props,
  data,
  computed,
  methods: {
    addItem,
    deleteItem
  }
}
```

#### Writing the CSS
Finally we will add some styling for our component with the `src/components/Checklist/style.css` file.

Our styling for this component is going to be simple, we want it to be held in a div
that is centered, has a rounded border, a bit of box-shadow, and some padding:

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

### ChecklistItem Component

The ChecklistItem component will be the individual listings that appear within the Checklist. We will start by creating a new folder `ChecklistItem` within our existing `Checklist` folder. As before, we will add the following 4 files:

1. ChecklistItem.vue
2. template.html
3. script.js
4. style.css

Matching the way we made the `Checklist` component, the `ChecklistItem.vue` file will look like this:

```html
<template src='./template.html'/>
<script src='./script.js'/>
<style src='./style.css'/>
```

#### Building the Template
First, open the template file located at `src/components/Checklist/ChecklistItem/template.html`.

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
- C) Button to remove the item from the Checklist

##### Item Finished Checkbox
We just need a simple checkbox that is bound to a data value of the component:

```html
<b-checkbox v-model='itemChecked'/>
```

##### Item Description Text
This will be a text span that will hold the description of the item:

```html
<span
  class='item-static'
  :class="{'item-checked': itemChecked}"
  v-show='!editingItem'
  @dblclick='editItem'
>
  {{itemTitleDone}}
</span>
```

A few things to take note of here:

- We are conditionally adding a class called 'item-checked' if the value of `itemChecked` is true
- We are only rendering this text span if `editingItem` is false; aka don't show this text if the item is being edited
- When the text span is double clicked, we are calling the `editItem` function

##### Edit Item Description Input
We will need a way to allow for an item's text to be edited, for that we will add a text input field:

```html
<!-- Edit Item Description Input -->
<b-input
  v-model='itemTitleEdit'
  v-show='editingItem'
  :disabled='itemChecked'
  @blur='doneEdit'
  @keyup.native.enter='doneEdit'
  @keyup.native.esc='cancelEdit'
  expanded
/>
```

Things to note:

- We only show this field when `editingItem` is true. In tandem with the logic of the text span, the input will seamlessly appear when the user double-clicks the text field
- The input is disabled if `itemChecked` is true
- The input will call `doneEdit` if the user presses enter, or clicks somewhere outside of the input
- The input will call `cancelEdit` if the user presses esc

##### Delete Item Button
The last thing we will need for this template is a button to remove the item from the `Checklist`

```html
<!-- Delete Item Button -->
<p class='control'>
  <button class='button is-danger' @click='deleteItem'>
    <b-icon icon='close'/>
  </button>
</p>
```

##### Finalizing
To finish the template, we will wrap all those pieces into a `<b-field>` tag

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
    expanded
  />

  <!-- Delete Item Button -->
  <p class='control'>
    <button class='button is-danger' @click='deleteItem'>
      <b-icon icon='close'/>
    </button>
  </p>
</b-field>
```

#### Writing the Script
Next, we will work on the JavaScript file located at `src/components/Checklist/ChecklistItem/script.js`.

##### Name
The name of the component will be `Checklist`:

```javascript
const name = 'ChecklistItem'
```

##### Components
We don't have any components to register for this component.

##### Props
The `ChecklistItem` component requires two props, which we get from the `checklistItems` list in the `Checklist` component.

```javascript
const props = {
  indexKey: { type: Number, required: true },
  itemTitle: { type: String, required: true }
}
```

##### Data
According to our template, we will need four properties in our data object:
- Whether or not the current item is checked
- Whether or not the current item is being edited
- The description of an item to show in the text span
- The description of an item to show in the edit item input field

```javascript
const data = function () {
  return {
    itemChecked: false,
    editingItem: false,
    itemTitleDone: this.itemTitle,
    itemTitleEdit: this.itemTitle
  }
}
```

##### Methods
The methods we will need for this component are operations to edit item, confirm edit, cancel edit, and delete item.

###### editItem
editItem will set the value of `editingItem` to true if the item hasn't already been checked.

```javascript
const editItem = function () {
  if (!this.itemChecked) {
    this.editingItem = true
  }
}
```

##### cancelItem
cancelItem will revert the item back to its previous description and hides the editing input field.

```javascript
const cancelEdit = function () {
  this.editingItem = false
  this.itemTitleEdit = this.itemTitleDone
}
```

##### doneEdit
doneEdit will confirm the edited changes, and will set the text value of the span to be equal to those
changes. If the value that is entered is blank, then the item will be deleted.

```javascript
const doneEdit = function () {
  this.itemTitleDone = this.itemTitleEdit
  this.editingItem = false
  if (this.itemTitleDone === '') {
    this.deleteItem()
  }
}
```

##### deleteItem
deleteItem will delete the item from the parent `Checklist` component.

```javascript
const deleteItem = function () {
  this.$parent.deleteItem(this.indexKey)
}
```

##### Exporting the script
With the script logic written, we need to export it in the same way you export in a standard Vue file:

```javascript
export default {
  name,
  components: {},
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

##### Finishing the Script

```javascript
// ==================
// NAME
// ==================
const name = 'ChecklistItem'

// ==================
// COMPONENTS
// ==================
const components = {}

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
const computed = {}

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
 * Deletes the item from the parent Checklist component
 */
const deleteItem = function () {
  this.$parent.deleteItem(this.indexKey)
}

export default {
  name,
  components,
  props,
  data,
  computed,
  methods: {
    editItem,
    cancelEdit,
    doneEdit,
    deleteItem
  }
}
```

#### Writing the CSS
Finally, we will add a bit of styling in the `src/components/Checklist/ChecklistItem/style.css` file.

Like the `Checklist` component, we will be keeping the styling simple here. We want the text span of the item to expand to fill the empty space within its container, and we want to give it a border to make the transition between editing and non-editing modes more seamless.

We also want to create a rule for the `item-checked` class, to put a line through the text of the completed item in the `Checklist`

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

Alright, our components are in place and 

This is what the `Checklist.vue` file would look like if you
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
import ChecklistItem from '@/components/Checklist/ChecklistItem/ChecklistItem'
export default {
  name: 'Checklist',
  components: {
    'ChecklistItem': ChecklistItem
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

and here is the `ChecklistItem.vue` file with the same standard format:

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
  name: 'ChecklistItem',
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