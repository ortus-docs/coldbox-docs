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

So let's create our first app: `coldbox create app MyApp`. This will scaffold the application and install ColdBox for you. Just start a server so we can see our application: ` server start --rewritesEnable`.  This command will start a server with URL rewrites enabled, open a web browser and give you a welcome to ColdBox page. That's it, you have just created your first application.

> **Tip** Type `coldbox create app help` to get help on all the options for creating ColdBox applications.

## My First Handler

Now let's create our first controller, which in ColdBox is called Event Handler, that says hello to you using CommandBox:

```bash
coldbox create handler name="hello" actions="index"
```

This will generate a new handler called `hello.cfc` inside of the `handlers` folder, a view called `index` in the `views/hello` folder and even an integreation test at `tests/specs/integration/helloTest.cfc`. Now go to the following URL to execute the generated action:

```
http://localhost:{port}/hello/index
```

You will now see a big hello.index page. You have now created your first handler and view combination.