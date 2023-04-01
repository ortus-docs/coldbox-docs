# My First ColdBox Application

CommandBox comes with a `coldbox create app` command that can enable you to create application skeletons using one of our official skeletons or [your own](../../digging-deeper/recipes/application-templates.md).  Here are the names of the common ones you can find in our Github Organization:

* **AdvancedScript**  (`default`): A script based advanced template
* **elixir** : A [ColdBox Elixir](https://coldbox-elixir.ortusbooks.com/) based template
* **ElixirBower** : A [ColdBox Elixir](https://coldbox-elixir.ortusbooks.com/) + Bower based template
* **ElixirVueJS** : A [ColdBox Elixir](https://coldbox-elixir.ortusbooks.com/) + Vue.js based template
* **rest**: A RESTFul services template
* **rest-hmvc**: A RESTFul service built with modules
* **Simple** : A traditional simple template
* **SuperSimple** : The bare-bones template

{% hint style="success" %}
You can find all our template skeletons here: [github.com/coldbox-templates](https://github.com/coldbox-templates)
{% endhint %}

{% embed url="https://coldbox-elixir.ortusbooks.com/" %}
ColdBox Elixir Docs
{% endembed %}

## Scaffolding Our Application

So let's create our first app using the _default_ template skeleton **AdvancedScript**:

```bash
coldbox create app Quickstart
```

This will scaffold the application and also install ColdBox for you. The following folders/files are generated for you:

```
+ coldbox // The ColdBox framework library (CommandBox Tracked)
+ config // Configuration files
+ handlers // Your handlers/controllers
+ includes // static assets
+ interceptors // global interceptors
+ layouts // Your layouts
+ lib // Java Jars to load
+ models // Your Models
+ modules // CommandBox Tracked Modules
+ modules_app // Custom modules
+ tests // Test harness
+ views // Your Views
+ Application.cfc // Bootstrap
+ box.json // CommandBox package descriptor
+ index.cfm // Front controller
```

Now let's start a server so we can see our application running:

```
server start
```

{% hint style="info" %}
This will start up a [Lucee](https://www.lucee.org) 5 open source CFML engine. If you would like an **Adobe ColdFusion** server then just add to the command: `cfengine=adobe@{version}` where `{version}` can be: `2021,2018,2016.`
{% endhint %}

### Default Event

This command will start a server with URL rewrites enabled, open a web browser for you and execute the `index.cfm` which in turn executes the **default event** by convention in a ColdBox application: `main.index`.   This is now our first convention!

Instead of executing pages like in a traditional application, we always execute the same page but distinguish the event we want via [URL routing](../../the-basics/routing/).  When no mappings are present we execute the default event by convention.

{% hint style="success" %}
**Tip:** ColdBox Events map to handlers (**cfc**) and appropriate actions (**functions**)
{% endhint %}

{% hint style="success" %}
**Tip**: The default event can be also changed in the configuration file: `config/Coldbox.cfc`
{% endhint %}



![](<../../.gitbook/assets/image (3).png>)



Hooray, we have scaffolded our first application, started a server and executed the default event.  Explore the application template generated, as it contains many useful information about your application.

{% hint style="success" %}
**Tip:** Type `coldbox create app help` to get help on all the options for creating ColdBox applications.
{% endhint %}

## File/Folder Conventions

ColdBox is a conventions based framework, meaning you don't have to explicitly write everything.  We have a few contracts in place that you must follow and boom, :tada: things happen. The location and names of files and functions matter. Since we scaffolded our first application, let's write down in a table below with the different conventions that exist in ColdBox.

| **File/Folder Convention** | **Mandatory** | **Description**                              |
| -------------------------- | ------------- | -------------------------------------------- |
| `config/Coldbox.cfc`       | false         | The application configuration file           |
| `config/Router.cfc`        | false         | The application URL router                   |
| `handlers`                 | false         | Event Handlers (controllers)                 |
| `layouts`                  | false         | Layouts                                      |
| `models`                   | false         | Model layer business objects                 |
| `modules`                  | false         | CommandBox Tracked Modules                   |
| `modules_app`              | false         | Custom Modules You Write                     |
| `tests`                    | false         | A test harness with runners, specs and more. |
| `views`                    | false         | Views                                        |

{% hint style="info" %}
What is the common denominator in all the conventions? \
**That they are all optional.**

Nothing is really mandatory in ColdBox anymore.
{% endhint %}

## Re-initializing The Application

There will be times when you make configuration or code changes that are not reflected immediately in the application due to caching. You can tell the framework to **reinit** or restart the application for you via the URL by leveraging the special URL variable `fwreinit`.

```
http://localhost:{port}/?fwreinit=1
```

You can also use CommandBox to **reinit** the application:

```bash
coldbox reinit
```

{% hint style="success" %}
**Tip:** You can add a password to the **reinit** procedures for further security, please see the [configuration section](https://github.com/ortus-docs/coldbox-docs/tree/7a8d2250f812e1b65cfc9c2888a8489110724897/the-basics/configuration/coldbox.cfc).
{% endhint %}
