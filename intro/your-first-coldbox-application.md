# My First ColdBox Application

CommandBox comes with a `coldbox create app` command that can enable you to create application skeletons using one of our official skeletons or [your own](/full/recipes/application_templates.md):

* **Advanced** (default) : A tag based advanced template* **AdvancedScript** : A script based advanced template* **elixir** : A ColdBox Elixir based template* **elixir-bower** : A ColdBox Elixir + Bower based template* **elixir-vuejs** : A ColdBox Elixir + Vue.js based template* **rest** : A RESTFul services template* **Simple** : A traditional simple template* **SuperSimple** : The bare-bones template


> You can find all our templates here: [github.com/coldbox-templates](https://github.com/coldbox-templates)

So let's create our first app using the _default_ template:

```bash
coldbox create app MyApp
```

This will scaffold the application and install ColdBox for you. Now let's start a server so we can see our application running:

```
server start --rewritesEnable
```

> **Note** This will start up a [Lucee](https://www.lucee.org) open source CFML engine. If you would like an Adobe ColdFusion server then just add to the command: `cfengine=adobe@{version}` where `{version}` can be: `2016,11,10,9`. 

This command will start a server with URL rewrites enabled, open a web browser for you and execute the default event by convention in a ColdBox application: `main.index`.

That's it, you have just created your first application.

> **Tip** Type `coldbox create app help` to get help on all the options for creating ColdBox applications.



## Event Handlers

<img src="/images/ColdBoxSimpleMVC.png">


Event handlers are the controller layer in ColdBox and is what you will be executing via the URL or a FORM post.  All event handlers are singletons, which means they are cached for the duration of the application.  Once you started the server and opened the browser, the default event got executed which maps to an event handler CFC (controller) `handlers/main.cfc` and the method/action in that CFC alled `index()`. Go open the `handler/main.cfc` and explore the code.

```
// Default Action
function index( event, rc, prc ){ 
    prc.welcomeMessage = "Welcome to ColdBox!";    
    event.setView( "main/index" );
}
```

Every action in ColdBox receives three arguments:

* `event` - An object that models and is used to work with the current request
* `rc` - A struct that contains both URL/FORM variables (unsafe data)
* `prc` - A secondary struct that is private only settable from within your application (safe data)

This line `event.setView( "main/index" )` told ColdBox to render a view back to the user found in `views/main/index.cfm` using a default layout, which by convention is called `Main.cfm` which can be found in the `/layouts` folder.


## My First Handler & View

Now let's create our first controller, which in ColdBox is called Event Handler.  Let's go to CommandBox again:

```bash
coldbox create handler name="hello" actions="index"
```

This will generate a new handler called `hello.cfc` inside of the `handlers` folder, a view called `index.cfm` in the `views/hello` folder and even an integration test at `tests/specs/integration/helloTest.cfc`. Now go to the following URL to execute the generated event:

```
# With rewrites enabled
http://localhost:{port}/hello/index

# With no rewrites enabled
http://localhost:{port}/index.cfm/hello/index

```

You will now see a big `hello.index` outputted to the screen. You have now created your first handler and view combination.  

Did you detect a convention here? The sections in the URL are the same as the name of the event handler CFC (`hello`) and method that was generated (`index`).  By convention, this is how you execute events in ColdBox by leveraging the following URL pattern:

```
http://localhost:{port}/folder/handler/action
http://localhost:{port}/handler/action
http://localhost:{port}/handler
```

If no `action` is defined then the default action of `index` will be used.

## My First Virtual Event

Now let's create a virtual event, which is basically just a view we want to execute with no event handler controller needed.

```bash
coldbox create view name="virtual/hello"
```

Open the view now (`/views/virtual/hello.cfm`) and add the following:

```html
<h1>Hello from ColdBox Land!</h1>
```

Then go execute the virtual event: 

```
http://localhost:{port}/virtual/hello
```

You will get the `Hello From ColdBox Land!` displayed!  This is a great way to create dynamic views or even bring in legacy/procedural templates into an MVC framework.

## Reiniting The Application

There will be times when you make configuration or disk changes that are not reflected immedidately in the application due to caching.  You can tell the framework to reinit or restart the application for you via the URL by leveraging the special URL variable `fwreinit` with any value.

```
http://localhost:{port}/?fwreinit=1
```