# My First ColdBox Application

CommandBox comes with a `coldbox create app` command that can enable you to create application skeletons using one of our official skeletons:

* *AdvancedScript (default)*
* Advanced (Tags)
* Rest
* Elixir
* Elixir-Bower
* Elixir-Vuejs
* Simple
* SuperSimple

> You can find all our templates here: [github.com/coldbox-templates](https://github.com/coldbox-templates)

So let's create our first app: 
```bash
coldbox create app MyApp
```

This will scaffold the application and install ColdBox for you. Just start a server so we can see our application: 

```
server start --rewritesEnable
```

> **Note** This will start up a Lucee open source CFML engine. If you would like an Adobe ColdFusion server then just add to the command: `cfengine=adobe@{version}` where `{version}` can be: `2016,11,10,9`. 

This command will start a server with URL rewrites enabled, open a web browser for you and execute the default event in a ColdBox application: `main.index`. That's it, you have just created your first application.

> **Tip** Type `coldbox create app help` to get help on all the options for creating ColdBox applications.

## My First Handler & View

Now let's create our first controller, which in ColdBox is called Event Handler, that says hello to you using CommandBox:

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

You will now see a big `hello.index` outputted to the screen. You have now created your first handler and view combination.  Did you detect a convention here? The sections in the URL are the same as the name of the event handler CFC (`hello`) and method that was generated (`index`).  By convention, this is how you execute events in ColdBox by leveraging the following URL pattern:

```
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

Then go execute the virtual event: `http://localhost:{port}/virtual/hello` and you will get the `Hello From ColdBox Land!` displayed!