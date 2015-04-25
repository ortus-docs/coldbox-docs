# Creating An Application

Now that we have ColdBox installed we can create our application.  CommandBox and ColdBox come bundled with some application templates:

* **Advanced** : A tag based advanced template
* **AdvancedScript** : A script based advanced template
* **rest** : A RESTFul services template
* **Simple** : A traditional simple template
* **SuperSimple** : The bare-bones template

We will be using the `AdvancedScript` template to start.  So go again into commandbox and type:

```bash
coldbox create app name=MyApp skeleton=AdvancedScript
```

> **Info** Remember you can use the tab-completion to get all the necessary input and arguments into the commands.

That will create a new ColdBox app configured and ready for you.

> **Tip** You can also leverage the `--installColdBox` and `--installColdBoxBE` flags to create and install ColdBox in one command.