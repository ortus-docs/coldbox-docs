# My First ColdBox Application

CommandBox comes with a `coldbox create app` command that can enable you to create application skeletons using one of our official skeletons or [your own](/full/recipes/application_templates.md):

* **Advanced** (default) : A tag based advanced template
* **AdvancedScript** : A script based advanced template
* **elixir** : A ColdBox Elixir based template
* **elixir-bower** : A ColdBox Elixir + Bower based template
* **elixir-vuejs** : A ColdBox Elixir + Vue.js based template
* **rest** : A RESTFul services template
* **Simple** : A traditional simple template
* **SuperSimple** : The bare-bones template


> You can find all our template skeletons here: [github.com/coldbox-templates](https://github.com/coldbox-templates)

So let's create our first app using the _default_ template skeleton _AdvancedScript_:

```bash
coldbox create app MyApp
```

This will scaffold the application and also install ColdBox for you. Now let's start a server so we can see our application running:

```
server start --rewritesEnable
```

> **Note** This will start up a [Lucee](https://www.lucee.org) 4.5 open source CFML engine. If you would like an Adobe ColdFusion server then just add to the command: `cfengine=adobe@{version}` where `{version}` can be: `2016,11,10,9`.

This command will start a server with URL rewrites enabled, open a web browser for you and execute the default event by convention in a ColdBox application: `main.index`.

![](/images/app_template.png)


That's it, you have just created your first application.

> **Tip** Type `coldbox create app help` to get help on all the options for creating ColdBox applications.





## Reiniting The Application

There will be times when you make configuration, code or disk changes that are not reflected immedidately in the application due to caching.  You can tell the framework to reinit or restart the application for you via the URL by leveraging the special URL variable `fwreinit` with any value.

```
http://localhost:{port}/?fwreinit=1
```