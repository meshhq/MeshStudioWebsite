---
title: "React Native Navigation"
sub_title: "Stacks on Tabs"
tags: ["React Native", "Mobile", "JavaScript"]
date: 2018-05-08T11:59:08-07:00
publishdate: 2018-05-08T11:59:08-07:00
hero_image: "/images/blog/2018-05-08-react-native-navigation/hero.png"
author:
  name: "Reid Weber"
  url: https://medium.com/@reidweber_34407
  mail: reid@meshstudio.io
  avatar: "https://avatars2.githubusercontent.com/u/8824846?s=460&v=4"
---

## React Native Navigation

### From Native to Cross-Platform

React Native navigation code can seem very odd to developers coming from Objective C or Swift. Whereas native code asks that developers sub class Navigation and Tab Bar controllers, React Native demands that app navigation must be scaffolded in a single constructor. 

This seemed more than a little odd when I first started doing react native developmetn. I was also frustrated to find that React Native's documentation doesn't really provide much guidance beyond the ‘Hello World’ set up. 

With this post, I aim to provide a clear guide to setting up your navigation logic in a React Native application. We will be using the awesome [react-navigation](https://reactnavigation.org/) framework to do so. This framework allows you to build a tab based application by using a `TabNavigator` object with `StackNavigator`’s embedded in each tab. I’ll also cover some tips on UI customization and passing props between navigators.

### Setup

For this post, we’ll be using [`create-react-native-app`](https://github.com/react-community/create-react-native-app) (CRNA) to get started. This cli uses the [Expo](https://expo.io/) framework to build and run React Native applications. 

We’ll also be running the app on the iOS simulator so you’ll need to [download Xcode](https://developer.apple.com/xcode/) if you don’t already have it. If you want to run your app on an actual device, you'll need to download the Expo app from the iOS App Store or Google Play, and follow the instructions displayed in you terminal after running `npm start`.

First off, download the CRNA cli by running:

```
npm install -g create-react-native-app
```

Next, you can create a clean project by running:

```
create-react-native-app MyApplication
```

Now `cd` into that directory and install `react-navigation`. 

```
npm install react-navigation 
```

Lets go ahead an fire up your application at this point to make sure everything is working correctly. You can run your project on an iOS simulator by running the following.

```
npm run ios
```

Notice that the home screen has a couple default labels. These are created in `App.js`. (Note: running on a Android simulator is a little more involved so we aren’t going to cover it in this post. Read more [here](https://developers.arcgis.com/android/10-2/sample-code/emulator/).)

### Setting Up A TabNavigator

WHAT IS A TAB NAVIGATOR??? 

Unlike a traditional React components, `react-navigation` components aren’t implemented as a Component subclass; they’re created via a custom constructor function that can take in a number of different options.

Let’s start by opening up our `App.js` file and removing the `<View />` component and the three `<Text />` components in `render()`. Now add the import statement at the top of the file.

```
import { createBottomTabNavigator } from 'react-navigation'
```

In order to create a `TabNavigator` component, that we can return from the `render()` method, we’ll need to call the `createBottomTabNavigator` constructor and set it to a variable. Here’s our new `App.js` file:

WHERE iS THE FILE???

You’ll see how navigation components are a little different in how they’re created. Instead of sub-classing the `React.Component` class, we’re calling the exported constructor function `createBottomTabNavigator` and passing through options that will define what the `TabNavigator` will display.

(Note: When naming components that are set to variables, be sure to begin the name with a capital letter. JSX will interpret a lowercase tag name to be an HTML tag. `<tabNavigator />` will compile to `React.createElement('tabNavigator')` where `<TabNavigator />` will compile to 
`React.createElement(TabNavigator)` — we require the latter.) 

We aren’t able to run this yet since we haven’t created `StackOne` and `StackTwo`. But that’s the next step!

### Setting Up The Stack Navigators

WHAT IS A STACK NAVIGATOR??? 

Start by running the following commands to create all the other files we’ll need for this project.

```
touch StackOne.js StackOneMain.js StackOneDetail.js StackTwo.js StackTwoMain.js StackTwoDetail.js
```

We’ll start with `StackOne.js` and `StackTwo.js` and create our `StackNavigators`.

Creating a `StackNavigator` is pretty similar to the `TabNavigator` in the sense that we’ll call a `react-navigation` function, pass through some options, and export the variable we set it to.

Here is `StackOne.js`:

```
import { createStackNavigator } from 'react-navigation'
import StackOneMain from './StackTwoMain'
import StackOneDetail from './StackTwoDetail'

const StackOne = createStackNavigator({
  Main: {
    screen: StackOneMain
  },
  Detail: {
    screen: StackOneDetail
  }
})

export {
  StackOne
}
```

Here is `StackTwo.js`:

```
import { createStackNavigator } from 'react-navigation'
import StackTwoMain from './StackTwoMain'
import StackTwoDetail from './StackTwoDetail'

const StackTwo = createStackNavigator({
  Main: {
    screen: StackTwoMain
  },
  Detail: {
    screen: StackTwoDetail
  }
})

export {
  StackTwo
}
```

This is pretty straightforward; the `createStackNavigator()` function takes in two components that it will navigate back and forth between.

Under the hood, `react-navigation` will add a `navigator` to the `props` of the component passed through in this function. Every time we create an instance of either `StackMain` or `StackDetail` we’ll be able to use `this.props.navigation` to reference the `StackNavigator`’s we created in the files above.

(Note: The keys used in the constructor will be used later on to navigate to the screen we want. `Main` and `Detail` can be substituted for any name you want — just keep track of them!)

We still aren’t able to properly run this until we have some child components defined for our `StackNavigator`'s. Let get to that now.

### Creating Stack Child Components

For this tutorial we’re going to use very simple components for our StackNavigator's. Each `Main` component will have a button in the middle that performs the navigation (we don’t need anything but a label on the detail screen since we get the back button for free).

Here are the component files:

`StackOneMain.js`

```
import React from 'react'
import {
  View,
  Text,
  StyleSheet,
  TouchableOpacity
} from 'react-native'

export default class StackOneMain extends React.Component {

  render() {
    return (
      <View style={ styles.container }>
        <TouchableOpacity onPress={ this.navigation }>
          <Text style={ styles.text }>Navigate</Text>
        </TouchableOpacity>
      </View>
    )
  }

  navigation = () => {
    this.props.navigation.navigate('Detail')
  }

}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center'
  },
  text: {
    fontSize: 24,
    color: '#2662c1'
  }
})
```

(Note: `StackTwoMain.js` is identical except the class name)

The string passed into the `this.props.navigator.navigate()` has to match the key we originally set in `createStackNavigator()`. For this tutorial, it’s 'Detail'. The object passed into the `this.props.navigator.navigate()` is used to pass props between components via the `navigator`. You can then access this data when the detail view mounts. See the code below in `StackOneDetail.js` to see how this data is pulled out with `this.props.navigation.state.params`.

Everything in this file is pretty simple. The one ‘gotcha’ in here is the navigation function needs to be in the ES7 ‘arrow-function’ style. This style will auto-bind the component instance to the function so we are able to access `navigation` using `this.props`. If you don’t do it this way the ‘this’ accessed when the function is called will be the instance of `TouchableOpacity` it was passed through too.

`StackOneDetail.js`

```
import React from 'react'
import {
  View,
  Text,
  StyleSheet
} from 'react-native'

export default class StackOneDetail extends React.Component {

  componentWillMount() {
    const navigationParams = this.props.navigation.state.params
    const navigationProp = navigationParams.navigationProp
    console.log('props ', navigationProp)
  }
	
  render() {
    return (
      <View style={ styles.container }>
        <Text style={ styles.text }>Detail!</Text>
      </View>
    )
  }

}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
		justifyContent: 'center'
  },
  text: {
    fontSize: 24,
    color: '#2662c1'
  }
})
```

(Note: `StackTwoDetail.js` is identical except the class name)

Another important note is that it is typically considered a best practice to decouple your `StyleSheets` from the Component files. We’re going to keep them in the Component file for simplicity’s sake in this tutorial.

We can now see our Navigators in action. Run the app on the iOS simulator again with:

```
npm run ios
```

You’ll see that the Navigate button will push the detail view onto the screen.

### Customization

You may have noticed that our tabs currently don’t have an icons on them. The `react-navigation` docs offer a [simple way](https://reactnavigation.org/docs/en/tab-based-navigation.html#customizing-the-appearance) to do this using `react-native-vector-icons` but we’re going to take it one step further and create a custom icon component that will offer us more flexibility.

We’re going to create a custom `TabIcon` component. Let’s start by entering the following:

```
touch TabIcon.js
```

This component will take in two props that will help us display the UI correctly. 

First, we’ll need pass through an `index` that we’ll use to display the correct image. Second, we’ll need an `isFocused` prop that will let us know if the given tab is currently selected.

Here is our new `TabIcon.js` file:

```
import React from 'react'
import {
  View,
  Text,
  Image,
  Platform,
  StyleSheet
} from 'react-native'

export default class TabIcon extends React.Component {

  render() {
    let image
    switch(this.props.index) {
      case 0:
        if (this.props.isFocused) {
          image = require('./resources/selected_event_tab.png')
        } else {
          image = require('./resources/event_tab.png')
        }
        break
      case 1:
      default:
        if (this.props.isFocused) {
          image = require('./resources/selected_profile_tab.png')
        } else {
          image = require('./resources/profile_tab.png')
        }
    }
    if (this.props.isFocused) {
      return (
        <View style={ style.container }>
          <Image style={ style.image } source={ image } />
        </View>
      )
    } else {
      return (
        <View style={ style.container }>
          <Image style={ style.image } source={ image } />
        </View>
      )
    }
  }

}

const style = StyleSheet.create({
	container: {
		flex: 1,
		alignItems: 'center',
    justifyContent: 'center'
	},
	image: {
		height: 18,
		width: 22,
		resizeMode: 'contain'
	}
})
```


(Note: I created a directory called `resources` and added four images to it. A ‘selected’ and ‘normal’ version of each icon. You can get these icons from the finished [repository](https://github.com/Reidweb1/RNTabBarDemo).)

You’ll see that we’re using the props passed through to determine which image will be shown. We’re switching on the `index` provided, and checking whether or not the component is the tab that `isFocused`.

Let’s return to our `App.js` file where the `TabNavigator` is created. We’re going to import our new `TabIcon` and pass it into the `createBottomTabNavigator()` via the `navigationOptions`.

Here is what our `App.js` file looks like now:

```
import React from 'react'
import { createBottomTabNavigator } from 'react-navigation'
import { StackOne } from './stacks/StackOne'
import { StackTwo } from './stacks/StackTwo'
import TabIcon from './TabIcon'
import { Text, Platform, StyleSheet } from 'react-native'

export default class App extends React.Component {
  
  render() {
    return (
      <HomeNavigation />
    )
  }

}

const HomeNavigation = createBottomTabNavigator(
  {
    TabOne: {
      screen: StackOne,
      navigationOptions: ({ navigation }) => ({
	      tabBarIcon: ({ focused, tintColor }) => {
		      return <TabIcon index={ 0 } isFocused={ focused }/>
        }
      })
    },
    TabTwo: {
      screen: StackTwo,
      navigationOptions: ({ navigation }) => ({
	      tabBarIcon: ({ focused, tintColor }) => {
		      return <TabIcon index={ 1 } isFocused={ focused }/>
        }
      })
    }
  }
)
```

It’s important to note that we’re using the `focused` param sent through the `tabBarIcon` function, which is embedded in the `navigatorOptions` function. Note that the `navigation` object is passed through at the top level of the `navigatorOptions` — we don’t need this at the moment, but it becomes very useful if you want custom components that are also able to navigate.

## Conclusion

Now your tab-based application won’t be stuck with static screens! While `react-navigation` may not be the most intuitive thing (especially for people used to sub-classing everything), it offers a lot of room for customization that has, historically, been a pain when it comes to native mobile UI. For a list of everything you can do with `navigationOptions` check out the docs [here](https://reactnavigation.org/docs/en/tab-navigator.html). If you can, check out the [repository](https://github.com/Reidweb1/RNTabBarDemo) we built in this tutorial here.