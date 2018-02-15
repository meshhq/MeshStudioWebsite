---
title: "Serving HTML with Golang"
sub_title: ""
tags: []
date: 2017-11-06T10:09:12-08:00
publishdate: 2017-11-06T10:09:12-08:00
hero_image: ""
author:
  name: "John Daly"
  url: ""
  mail: "john@meshstudio.io"
  avatar: "https://avatars2.githubusercontent.com/u/1719443?s=460&v=4"
---

Here at Mesh, we like to keep up-to-date with the latest trends in software development; often trying out various programming languages and frameworks. One of the languages that we like to use is [Go](http://golang.org). We appreciate that Go is strongly-typed, is opinionated about formatting, and is strict about keeping the structure of programs simple. It is also quite a versatile language, so in this post we will explore how it can be used to create an HTML server using [**gorilla/mux**](http://github.com/gorilla/mux), [**jteeuwen/go-bindata**](http://github.com/jteeuwen/go-bindata), and [**html/template**](https://golang.org/pkg/html/template/).

Check out the source of this project here: https://github.com/meshhq/golang-html-template-tutorial


### Project Structure
```
// Auto-generated files to bundle templates/style-sheets together
- assets
  - assets.go
  - bindata.go

// Static files, which will be style-sheets for this project
- static
  - navigation_bar.css
  - style.css
  - third_view.css

// HTML templates, which will be rendered and served
- templates
  - index.html
  - navigation_bar.html
  - second_view.html
  - third_view.html

// Bulk of logic goes here
- main.go

// Route-specific logic for the third_view template
- third_view.go
```
The bulk of the logic for this project will be in the **main.go** file, which consists of the following pieces:

1. Initialization of HTML Templates
2. Configuration of the Server
3. Starting and Stopping the Server
4. Route Handling and Rendering Templates

Let’s dive into these!

### Initialization of HTML Templates
At the top of our main.go file, we will declare and initialize the HTML templates:

<script src="https://gist.github.com/JohnDaly/84ec88df7e82ff2676477afa97a6ca74.js"></script>

The templates are loaded in from the **assets/bindata.go** file, which is a file that includes all the HTML and CSS information for the app; it is auto-generated using the **jteeuwen/go-bindata** utility. You will notice that **thirdViewTpl** includes a function map called **thirdViewFuncMap**, this will be discussed further in a bit.

### Configuration of the Server
Next we will fill out the **main()** function, which consists of setting up the server configuration, starting the server, and listening for a signal to stop the program:

<script src="https://gist.github.com/JohnDaly/a4c719b1499544c76f5a994b71b27851.js"></script>

Notice that we are setting the ‘Host’ of the configuration to be ‘localhost:5000’, you can change this to whatever you like.

### Starting the Server
To start the server, we create a function that will set-up the router, create the server object, and then start a listener:

<script src="https://gist.github.com/JohnDaly/69a8fdd1140ee2bf6b89c787ece423b8.js"></script>

An important thing to take note of here is the router setup. You can see that we are setting up four total routes; the first three are responsible for rendering the appropriate HTML templates, and the fourth one will serve up files that live in the **/static** folder. These static files will consist of the CSS files for our project.

### Stopping the Server
To stop the server, we attempt to gracefully shut it down with a timeout. If the shutdown process does not finish within this timeout, then we force close the server:

<script src="https://gist.github.com/JohnDaly/ffde395fcc4561b1f3de3e29d5209ac2.js"></script>

### Route Handling and Rendering Templates
The code that actually renders the HTML templates happens here:

<script src="https://gist.github.com/JohnDaly/c92ff4c5d7aa4efb69f994c9fdb29f12.js"></script>

The **HomeHandler** and **SecondHandler** are very similar, so I will go over how they are set up. Let’s start by talking about how we deal with our CSS files.

### HTTP/2 Push
You might have noticed that at the beginning of the functions, we make calls to push files. We are making use of a new feature introduced in HTTP2 (you can read more about it here), which allows your server to push assets into the client’s browser cache without the need for an additional request. This allows for you cut down the number of requests that are needed from your clients; they make one call and receive everything they need in one go. Not all clients will allow for push to be used, but its a nice feature to have on your server to cut down on the number of client requests. Here is what the **push** function looks like:

<script src="https://gist.github.com/JohnDaly/08726cd836560aee3b2e0be63b261376.js"></script>

It should be noted that **push** functionality is supported in Go 1.8 and higher, so be sure to check that you are using an appropriate version.

All three routes in this example app will be pushing the **style.css** and **navigation_bar.css** files. The **style.css** file contains common styles, which are shared across multiple HTML files in our project. We are also pushing the **navigation_bar.css** file, because each of these routes will also be rendering a navigation bar.

The navigation bar is passed into the templates via the **fullData** object, which contains information that we can render into the templates. Before we move on to talk about the **ThirdHandler** logic, let’s take a look at the HTML template for the **HomeHandler**:

<script src="https://gist.github.com/JohnDaly/0f3f4a3d6cef093ce870de0f3861e551.js"></script>

In order to access the data that is passed into the **render** function, you use curly braces: {{ }}. You can see that we are accessing the **NavigationBar** data that was passing in with the following line: {{.NavigationBar}}.

The ‘.’ is referred to as the **cursor** or **dot**; this is important, as it represents your current location within the rendering process. In this example, the **dot** is at the top level, and has access to the **NavigationBar** key of the data object. To give an example of how the **dot** represents your location, consider the following example:

The following data is being passed into the render function:
```
{
  List: [
    { ID: 1 },
    { ID: 2 },
    { ID: 3 },
  ]
}
```
Let’s say we want to iterate over this list, and we want to render it into HTML. Here is what the logic would look like within the template file:
```
<ul>
  {{range .List}}
    <li>{{.ID}}</li>
  {{end}}
</ul>
```
When this template is rendered, the HTML will look like this:
```
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
<ul>
```
You can see how the **dot** represents our location within the rendering process here. For the outermost set of brackets, we have top-level access (we can access the **List** key). Once we enter the ‘{{range .List}} {{end}}’ block, the cursor moves down a level into **List**. For more reading on this, check out the documentation [here](https://golang.org/pkg/text/template).

Alright, let’s check out this third handler. Remember that the route for this handler was set up in the handler like so:
```
router.HandleFunc("/third/{number}", ThirdHandler)
```
The **{number}** piece of this is what is import to note; this is a variable that we can access in the handler.

The other thing to remember is that the template for this route was set up to include a map of functions:
```
thirdViewTpl = template.Must(
  template
    .New("third_view")
    .Funcs(thirdViewFuncMap)
    .Parse(thirdViewHTML)
)
```
We will discuss the actual map of functions being used in a bit, but with all this in mind, let’s take a look at the handler’s logic itself.

<script src="https://gist.github.com/JohnDaly/182fbdcb4b09a436f61a99029b26edcd.js"></script>

For this template, we are interested in the value passed into the route via the **‘number’** path variable. To get this value, we pass the request object into **mux.Vars()**. The call to **mux.Vars()** returns a map of the path parameters, so we will need to make a call to select the key we want. For this example, we want this value to be a number, so we attempt to convert the string that we get from the call to **mux.Vars(&#114;)**. You can see that is there is an error, we are setting the **queryString** variable to be the value of whatever string was passed in. This is so that we can render a different message in the HTML, as you will see.

Let’s take a look at the HTML template for this route:

<script src="https://gist.github.com/JohnDaly/09c9d2d33359906cdc6173486e3921f2.js"></script>

You can see that we are checking for the presence of **QueryString** in the data that is getting passed in; if this value is not nil, then we know that the user did not supply a number. If **QueryString** is nil, then we can display the HTML that will show the number entered and whether or not it is even or odd. The final piece to look at here is the following line:
```
{{.Number | formatOddOrEven}}
```
That map of functions we included with the template, when it was initialized, comes into play here. Let’s take a look at that map of functions, which can be found in **third_view.go**

<script src="https://gist.github.com/JohnDaly/3d060dc8847e76a1adfc02b8bf3e3554.js"></script>

We can see that the function map defined in **ThirdViewFormattingFuncMap** contains a function called **formatOddOrEven**; by piping the value of **.Number** into this function, we are calling **formatOddOrEven(Number)** and rendering the string it returns.

As a final note on rending HTML templates, it is best to keep the HTML file itself relatively simple, and keep most of the logic on the server-side. The Go template package provides building blocks (conditionals, comparison operators, basic iteration, etc), but most of the heavy lifting should be handled outside of the HTML itself.

### Running the Server
Okay, now that we have a better understanding of how this project it set up, let’s build it and see it in action. Before we do this, we need to do one more thing. In order for our templates to be initialized, we need to use **jteeuwen/go-bindata** to generate a file with all of our HTML template and CSS information. First we need to install the library:
```
go get -u github.com/jteeuwen/go-bindata/...
```
Next, we run the go-bindata tool to generate our file. Do this in the project’s root directory.
```
go-bindata -o=assets/bindata.go --nocompress --nometadata --pkg=assets templates/... static/...
```
Check that the **bindata.go** file was successfully generated in the assets folder of the project. Finally run this command to start the server:
```
go build && ./golang-html-template-tutorial
```
To see it running, point your browser to whatever the host is (by default, this should be localhost:5000)

We are only really just scratching the surface of some of the cool things you can do with Go, so consider this a very basic starting point. That being said, we hope that this can help you on your way to building your own Go projects!