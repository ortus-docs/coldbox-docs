# Creating An Application

Now that we have ColdBox installed we can create our application.  CommandBox and ColdBox come bundled with some easy to use application templates:

* **Advanced** : A tag based advanced template
* **AdvancedScript** : A script based advanced template
* **elixir** : A ColdBox Elixir based template
* **elixir-vuejs** : A ColdBox Elixir + Vue.js based template
* **rest** : A RESTFul services template
* **Simple** : A traditional simple template
* **SuperSimple** : The bare-bones template

All of these templates are stored in our Github organization: [coldbox-templates](https://github.com/coldbox-templates).  You can send us pull requests or create more templates if you like.

---

We will be using the `AdvancedScript` template to start.  So go again into CommandBox and type:

```bash
coldbox create app name=MyApp skeleton=AdvancedScript
```

This is what it will generate:

```
+coldbox
+config
+handlers
+includes
+interceptors
+layouts
+lib
+models
+modules
+remote
+tests
+views
+ .project
+Application.cfc
+box.json
+favicon.ico
+index.cfm
+robots.txt
```

> **Info** Remember you can use the tab-completion to get all the necessary input and arguments into the commands.  You can also use the `app-wizard` command and CommandBox will prompt for all your options in a nice CLI wizard. Try it!

<br>

> **Tip** You can also leverage the `--installColdBox` and `--installColdBoxBE` flags to create and install ColdBox in one command.

As you can see it creates all the necessary folders for you to work with. We are now ready to start our application and spice it up.