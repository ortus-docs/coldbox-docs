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
Dir           0 Apr 25,2015 11:04:05 coldbox
Dir           0 Apr 25,2015 11:04:11 config
Dir           0 Apr 25,2015 11:04:11 handlers
Dir           0 Apr 25,2015 11:04:11 includes
Dir           0 Apr 25,2015 11:04:11 interceptors
Dir           0 Apr 25,2015 11:04:11 layouts
Dir           0 Apr 25,2015 11:04:11 lib
Dir           0 Apr 25,2015 11:04:11 models
Dir           0 Apr 25,2015 11:04:11 modules
Dir           0 Apr 25,2015 11:04:11 remote
Dir           0 Apr 25,2015 11:04:11 tests
Dir           0 Apr 25,2015 11:04:11 views
File H       269 Apr 25,2015 11:04:11 .project
File       1920 Apr 13,2015 18:04:34 Application.cfc
File        112 Apr 25,2015 11:04:05 box.json
File       1150 Jan 27,2015 10:01:04 favicon.ico
File        392 Jul 10,2013 14:07:56 index.cfm
File        471 Jan 15,2015 14:01:18 robots.txt
```

> **Info** Remember you can use the tab-completion to get all the necessary input and arguments into the commands.  You can also use the `app-wizard` command and CommandBox will prompt for all your options in a nice CLI wizard. Try it!

<br>

> **Tip** You can also leverage the `--installColdBox` and `--installColdBoxBE` flags to create and install ColdBox in one command.

As you can see it creates all the necessary folders for you to work with. We are now ready to start our application and spice it up.