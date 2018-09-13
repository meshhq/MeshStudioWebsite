---
title: "Serving HTML with Golang"
sub_title: "How to write your next web app in Golang!"
tags: ["Go", "WebApp", "Programming"]
date: 2017-11-06T10:09:12-08:00
publishdate: 2017-11-06T10:09:12-08:00
hero_image: /images/blog/2017-11-06-serving-html-with-golang/hero.jpg
author:
  name: "John Daly"
  url: "http://johnmcdaly.com/"
  mail: "john@meshstudio.io"
  avatar: "https://avatars2.githubusercontent.com/u/1719443?s=460&v=4"
---

Here at Mesh, we like to keep up-to-date with the latest trends in software development; often trying out various programming languages and frameworks. One of the languages that we like to use is [Go](http://golang.org). We appreciate that Go is strongly-typed, is opinionated about formatting, and is strict about keeping the structure of programs simple. It is also quite a versatile language, so in this post we will explore how it can be used to create an HTML server using [gorilla/mux](http://github.com/gorilla/mux), [jteeuwen/go-bindata](http://github.com/jteeuwen/go-bindata), and [html/template](https://golang.org/pkg/html/template/).

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
The bulk of the logic for this project will be in the `main.go` file, which consists of the following pieces:

1. Initialization of HTML Templates
2. Configuration of the Server
3. Starting and Stopping the Server
4. Route Handling and Rendering Templates

Let’s dive into these!

### Initialization of HTML Templates
At the top of our main.go file, we will declare and initialize the HTML templates:

```go
// Templates
var navigationBarHTML string
var homepageTpl *template.Template
var secondViewTpl *template.Template
var thirdViewTpl *template.Template

func init() {
	navigationBarHTML = assets.MustAssetString("templates/navigation_bar.html")

	homepageHTML := assets.MustAssetString("templates/index.html")
	homepageTpl = template.Must(template.New("homepage_view").Parse(homepageHTML))

	secondViewHTML := assets.MustAssetString("templates/second_view.html")
	secondViewTpl = template.Must(template.New("second_view").Parse(secondViewHTML))

	thirdViewFuncMap := ThirdViewFormattingFuncMap()
	thirdViewHTML := assets.MustAssetString("templates/third_view.html")
	thirdViewTpl = template.Must(template.New("third_view").Funcs(thirdViewFuncMap).Parse(thirdViewHTML))
}
```

The templates are loaded in from the `assets/bindata.go` file, which is a file that includes all the HTML and CSS information for the app; it is auto-generated using the `jteeuwen/go-bindata` utility. You will notice that `thirdViewTpl` includes a function map called `thirdViewFuncMap`, this will be discussed further in a bit.

### Configuration of the Server
Next we will fill out the `main()` function, which consists of setting up the server configuration, starting the server, and listening for a signal to stop the program:

```go
func main() {
	serverCfg := Config{
		Host:         "localhost:5000",
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 5 * time.Second,
	}
	htmlServer := Start(serverCfg)
	defer htmlServer.Stop()

	sigChan := make(chan os.Signal, 1)
	signal.Notify(sigChan, os.Interrupt)
	<-sigChan

	fmt.Println("main : shutting down")
}
```

Notice that we are setting the ‘Host’ of the configuration to be ‘localhost:5000’, you can change this to whatever you like.

### Starting the Server
To start the server, we create a function that will set-up the router, create the server object, and then start a listener:

```go
// Start launches the HTML Server
func Start(cfg Config) *HTMLServer {
	// Setup Context
	_, cancel := context.WithCancel(context.Background())
	defer cancel()

	// Setup Handlers
	router := mux.NewRouter()
	router.HandleFunc("/", HomeHandler)
	router.HandleFunc("/second", SecondHandler)
	router.HandleFunc("/third/{number}", ThirdHandler)
	router.PathPrefix("/static/").Handler(http.StripPrefix("/static/", http.FileServer(http.Dir("./static"))))

	// Create the HTML Server
	htmlServer := HTMLServer{
		server: &http.Server{
			Addr:           cfg.Host,
			Handler:        router,
			ReadTimeout:    cfg.ReadTimeout,
			WriteTimeout:   cfg.WriteTimeout,
			MaxHeaderBytes: 1 << 20,
		},
	}

	// Add to the WaitGroup for the listener goroutine
	htmlServer.wg.Add(1)

	// Start the listener
	go func() {
		fmt.Printf("\nHTMLServer : Service started : Host=%v\n", cfg.Host)
		htmlServer.server.ListenAndServe()
		htmlServer.wg.Done()
	}()

	return &htmlServer
}
```

An important thing to take note of here is the router setup. You can see that we are setting up four total routes; the first three are responsible for rendering the appropriate HTML templates, and the fourth one will serve up files that live in the `/static` folder. These static files will consist of the CSS files for our project.

### Stopping the Server
To stop the server, we attempt to gracefully shut it down with a timeout. If the shutdown process does not finish within this timeout, then we force close the server:

```go
// Stop turns off the HTML Server
func (htmlServer *HTMLServer) Stop() error {
	// Create a context to attempt a graceful 5 second shutdown.
	const timeout = 5 * time.Second
	ctx, cancel := context.WithTimeout(context.Background(), timeout)
	defer cancel()

	fmt.Printf("\nHTMLServer : Service stopping\n")

	// Attempt the graceful shutdown by closing the listener
	// and completing all inflight requests
	if err := htmlServer.server.Shutdown(ctx); err != nil {
		// Looks like we timed out on the graceful shutdown. Force close.
		if err := htmlServer.server.Close(); err != nil {
			fmt.Printf("\nHTMLServer : Service stopping : Error=%v\n", err)
			return err
		}
	}

	// Wait for the listener to report that it is closed.
	htmlServer.wg.Wait()
	fmt.Printf("\nHTMLServer : Stopped\n")
	return nil
}
```

### Route Handling and Rendering Templates
The code that actually renders the HTML templates happens here:
```go
// HomeHandler renders the homepage view template
func HomeHandler(w http.ResponseWriter, r *http.Request) {
	push(w, "/static/style.css", "style")
	push(w, "/static/navigation_bar.css", "style")
	w.Header().Set("Content-Type", "text/html; charset=utf-8")

	fullData := map[string]interface{}{
		"NavigationBar": template.HTML(navigationBarHTML),
	}
	render(w, r, homepageTpl, "homepage_view", fullData)
}

// SecondHandler renders the second view template
func SecondHandler(w http.ResponseWriter, r *http.Request) {
	push(w, "/static/style.css", "style")
	push(w, "/static/navigation_bar.css", "style")
	w.Header().Set("Content-Type", "text/html; charset=utf-8")

	fullData := map[string]interface{}{
		"NavigationBar": template.HTML(navigationBarHTML),
	}
	render(w, r, secondViewTpl, "second_view", fullData)
}

// ThirdHandler renders the third view template
func ThirdHandler(w http.ResponseWriter, r *http.Request) {
	push(w, "/static/style.css", "style")
	push(w, "/static/navigation_bar.css", "style")
	w.Header().Set("Content-Type", "text/html; charset=utf-8")

	var queryString string
	pathVariables := mux.Vars(r)
	queryNumber, err := strconv.Atoi(pathVariables["number"])
	if err != nil {
		queryString = pathVariables["number"]
	}
	fullData := map[string]interface{}{
		"NavigationBar": template.HTML(navigationBarHTML),
		"Number":        queryNumber,
		"StringQuery":   queryString,
	}
	render(w, r, thirdViewTpl, "third_view", fullData)
}
```

The `HomeHandler` and `SecondHandler` are very similar, so I will go over how they are set up. Let’s start by talking about how we deal with our CSS files.

### HTTP/2 Push
You might have noticed that at the beginning of the functions, we make calls to push files. We are making use of a new feature introduced in HTTP2 (you can read more about it here), which allows your server to push assets into the client’s browser cache without the need for an additional request. This allows for you cut down the number of requests that are needed from your clients; they make one call and receive everything they need in one go. Not all clients will allow for push to be used, but its a nice feature to have on your server to cut down on the number of client requests. Here is what the `push` function looks like:

```go
// Push the given resource to the client.
func push(w http.ResponseWriter, resource string) {
	pusher, ok := w.(http.Pusher)
	if ok {
		if err := pusher.Push(resource, nil); err == nil {
			return
		}
	}
}
```

It should be noted that `push` functionality is supported in Go 1.8 and higher, so be sure to check that you are using an appropriate version.

All three routes in this example app will be pushing the `style.css` and `navigation_bar.css` files. The `style.css` file contains common styles, which are shared across multiple HTML files in our project. We are also pushing the `navigation_bar.css` file, because each of these routes will also be rendering a navigation bar.

The navigation bar is passed into the templates via the `fullData` object, which contains information that we can render into the templates. Before we move on to talk about the `ThirdHandler` logic, let’s take a look at the HTML template for the `HomeHandler`:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <!-- Required meta tags -->
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Golang HTML Server</title>

        <!-- Bootstrap CSS -->
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">

        <link rel="stylesheet" href="/static/style.css">
        <link rel="stylesheet" href="/static/navigation_bar.css">
    </head>
    <body>
        <div class="container">
            {{.NavigationBar}}

            <h1>Hello, World!</h1>
        </div>
    </body>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.11.0/umd/popper.min.js" integrity="sha384-b/U6ypiBEHpOf/4+1nzFpr53nxSS+GLCkfwBdFNTxtclqqenISfwAzpKaMNFNmj4" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/js/bootstrap.min.js" integrity="sha384-h0AbiXch4ZDo7tp9hKZ4TsHbi047NrKGLO3SEJAg45jXxnGIfYzk4Si90RDIqNm1" crossorigin="anonymous"></script>
</html>
```

In order to access the data that is passed into the `render` function, you use curly braces: {{ }}. You can see that we are accessing the `NavigationBar` data that was passing in with the following line: 
```go
{{.NavigationBar}}
```

The ‘.’ is referred to as the `cursor` or `dot`; this is important, as it represents your current location within the rendering process. In this example, the `dot` is at the top level, and has access to the `NavigationBar` key of the data object. To give an example of how the `dot` represents your location, consider the following example:

The following data is being passed into the render function:
```json
{
  "List": [
    { "ID": 1 },
    { "ID": 2 },
    { "ID": 3 },
  ]
}
```
Let’s say we want to iterate over this list, and we want to render it into HTML. Here is what the logic would look like within the template file:
```html
<ul>
  {{range .List}}
    <li>{{.ID}}</li>
  {{end}}
</ul>
```
When this template is rendered, the HTML will look like this:
```html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
<ul>
```
You can see how the `dot` represents our location within the rendering process here. For the outermost set of brackets, we have top-level access (we can access the `List` key). Once we enter the ‘{{range .List}} {{end}}’ block, the cursor moves down a level into `List`. For more reading on this, check out the documentation [here](https://golang.org/pkg/text/template).

Alright, let’s check out this third handler. Remember that the route for this handler was set up in the handler like so:
```go
router.HandleFunc("/third/{number}", ThirdHandler)
```
The `{number}` piece of this is what is import to note; this is a variable that we can access in the handler.

The other thing to remember is that the template for this route was set up to include a map of functions:
```go
thirdViewTpl = template.Must(
  template
    .New("third_view")
    .Funcs(thirdViewFuncMap)
    .Parse(thirdViewHTML)
)
```
We will discuss the actual map of functions being used in a bit, but with all this in mind, let’s take a look at the handler’s logic itself.

```go
// ThirdHandler renders the third view template
func ThirdHandler(w http.ResponseWriter, r *http.Request) {
	push(w, "/static/style.css", "style")
	push(w, "/static/navigation_bar.css", "style")
	push(w, "/static/third_view.css", "style")
	w.Header().Set("Content-Type", "text/html; charset=utf-8")

	var queryString string
	pathVariables := mux.Vars(r)
	queryNumber, err := strconv.Atoi(pathVariables["number"])
	if err != nil {
		queryString = pathVariables["number"]
	}
	fullData := map[string]interface{}{
		"NavigationBar": template.HTML(navigationBarHTML),
		"Number":        queryNumber,
		"StringQuery":   queryString,
	}
	render(w, r, thirdViewTpl, "third_view", fullData)
}
```

For this template, we are interested in the value passed into the route via the `number` path variable. To get this value, we pass the request object into `mux.Vars()`. The call to `mux.Vars()` returns a map of the path parameters, so we will need to make a call to select the key we want. For this example, we want this value to be a number, so we attempt to convert the string that we get from the call to `mux.Vars(r)`. You can see that is there is an error, we are setting the `queryString` variable to be the value of whatever string was passed in. This is so that we can render a different message in the HTML, as you will see.

Let’s take a look at the HTML template for this route:

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <!-- Required meta tags -->
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Golang HTML Server</title>

        <!-- Bootstrap CSS -->
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">

        <link rel="stylesheet" href="/static/style.css">
        <link rel="stylesheet" href="/static/navigation_bar.css">
        <link rel="stylesheet" href="/static/third_view.css">
    </head>
    <body>
        <div class="container">
            {{.NavigationBar}}

            <h1>Rendering Data</h1>
            <h4>This page takes the integer passed in and determines if it is odd or even</h4>
            <div class="result-box">
                {{if .StringQuery}}
                    <h2 class="result-underlined">You didn't enter an integer. This is what was entered:</h2>
                    <h3>{{.StringQuery}}</h3>
                {{else}}
                    <h2 class="result-underlined">The number entered is:</h2>
                    <h3>{{.Number}}</h3>
                    <h2 class="result-underlined">This number is:</h2>
                    <h3>{{.Number | formatOddOrEven}}</h3>
                {{end}}
            </div>
        </div>
    </body>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.11.0/umd/popper.min.js" integrity="sha384-b/U6ypiBEHpOf/4+1nzFpr53nxSS+GLCkfwBdFNTxtclqqenISfwAzpKaMNFNmj4" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/js/bootstrap.min.js" integrity="sha384-h0AbiXch4ZDo7tp9hKZ4TsHbi047NrKGLO3SEJAg45jXxnGIfYzk4Si90RDIqNm1" crossorigin="anonymous"></script>
</html>
```

You can see that we are checking for the presence of `QueryString` in the data that is getting passed in; if this value is not nil, then we know that the user did not supply a number. If `QueryString` is nil, then we can display the HTML that will show the number entered and whether or not it is even or odd. The final piece to look at here is the following line:
```go
{{.Number | formatOddOrEven}}
```
That map of functions we included with the template, when it was initialized, comes into play here. Let’s take a look at that map of functions, which can be found in `third_view.go`

```go
// ThirdViewFormattingFuncMap returns a map of functions
// that will be used by the Third View template
func ThirdViewFormattingFuncMap() template.FuncMap {
	return template.FuncMap{
		"formatOddOrEven": formatOddOrEven,
	}
}

func formatOddOrEven(number int) string {
	remainder := int(math.Abs(float64(number))) % 2
	if remainder == 1 {
		return "Odd"
	}
	return "Even"
}
```

We can see that the function map defined in `ThirdViewFormattingFuncMap` contains a function called `formatOddOrEven`; by piping the value of `.Number` into this function, we are calling `formatOddOrEven(Number)` and rendering the string it returns.

As a final note on rending HTML templates, it is best to keep the HTML file itself relatively simple, and keep most of the logic on the server-side. The Go template package provides building blocks (conditionals, comparison operators, basic iteration, etc), but most of the heavy lifting should be handled outside of the HTML itself.

### Running the Server
Okay, now that we have a better understanding of how this project it set up, let’s build it and see it in action. Before we do this, we need to do one more thing. In order for our templates to be initialized, we need to use `jteeuwen/go-bindata` to generate a file with all of our HTML template and CSS information. First we need to install the library:
```bash
go get -u github.com/jteeuwen/go-bindata/...
```
Next, we run the go-bindata tool to generate our file. Do this in the project’s root directory.
```bash
go-bindata -o=assets/bindata.go --nocompress --nometadata --pkg=assets templates/... static/...
```
Check that the `bindata.go` file was successfully generated in the assets folder of the project. Finally run this command to start the server:
```bash
go build && ./golang-html-template-tutorial
```
To see it running, point your browser to whatever the host is (by default, this should be `localhost:5000`)

We are only really just scratching the surface of some of the cool things you can do with Go, so consider this a very basic starting point. That being said, we hope that this can help you on your way to building your own Go projects!