# My First ColdBox Application

The `coldbox create app` command enables you to create application skeletons using one of our official skeletons or [your own](../../digging-deeper/recipes/application-templates.md).  Here are the names of the common ones you can find in our Github Organization:

* **Default**: The default app template
* **Elixir** : A [ColdBox Elixir](https://coldbox-elixir.ortusbooks.com/) based template to do asset compilation for you
* **Rest**: A RESTFul services template
* **Rest-hmvc**: A RESTFul service built with modules
* **SuperSimple** : The bare-bones template
* **Vite:** The default template using VITE for asset bundling

{% hint style="success" %}
You can find all our template skeletons here: [github.com/coldbox-templates](https://github.com/coldbox-templates)
{% endhint %}

{% embed url="https://coldbox-elixir.ortusbooks.com/" %}
ColdBox Elixir Docs
{% endembed %}

## Scaffolding Our Application

So let's create our first app using the _default_ template skeleton:

```bash
coldbox create app Quickstart
```

### Conventions

Here are some of the major files and directory conventions you should know about:

<table><thead><tr><th>Directory</th><th data-type="checkbox">Convention</th><th data-type="checkbox">Mandatory</th><th>Description</th></tr></thead><tbody><tr><td>.vscode</td><td>false</td><td>false</td><td>Mappings and build tasks for VSCode</td></tr><tr><td>build</td><td>false</td><td>false</td><td>Docker helpers</td></tr><tr><td>coldbox</td><td>false</td><td>true</td><td>The framework library</td></tr><tr><td>config</td><td>true</td><td>true</td><td>Configurations and module configurations</td></tr><tr><td>handlers</td><td>true</td><td>false</td><td>Event handler controllers</td></tr><tr><td>includes</td><td>true</td><td>false</td><td>i18n, JavaScript, helpers, CSS</td></tr><tr><td>interceptors</td><td>false</td><td>false</td><td>Event driven listeners go here</td></tr><tr><td>layouts</td><td>true</td><td>false</td><td>Application CFML layouts</td></tr><tr><td>lib</td><td>true</td><td>false</td><td>Java libraries or third party libraries</td></tr><tr><td>models</td><td>true</td><td>false</td><td>Model objects</td></tr><tr><td>modules</td><td>true</td><td>false</td><td>CommandBox driven dependencies</td></tr><tr><td>modules_app</td><td>true</td><td>false</td><td>Custom applicaiton modules</td></tr><tr><td>tests</td><td>false</td><td>false</td><td>Your application specs</td></tr><tr><td>views</td><td>true</td><td>false</td><td>Application CFML views</td></tr></tbody></table>

Here are the major files you should know about:

<table><thead><tr><th>File</th><th data-type="checkbox">Convention</th><th data-type="checkbox">Mandatory</th><th>Description</th></tr></thead><tbody><tr><td>.cfconfig.json</td><td>true</td><td>false</td><td>Loads the CFML Engine settings</td></tr><tr><td>.cfformat.json</td><td>true</td><td>false</td><td>Formatting rules</td></tr><tr><td>.cflintrc</td><td>true</td><td>false</td><td>Linting rules</td></tr><tr><td>.env</td><td>true</td><td>false</td><td>Environment variables (Never commit)</td></tr><tr><td>.env.example</td><td>false</td><td>false</td><td>Example env file</td></tr><tr><td>Application.cfc</td><td>true</td><td>true</td><td>Your application bootstrap</td></tr><tr><td>box.json</td><td>true</td><td>false</td><td>Your CommandBox package descriptor</td></tr><tr><td>index.cfm</td><td>true</td><td>true</td><td></td></tr><tr><td>server.json</td><td>true</td><td>false</td><td></td></tr></tbody></table>

Now let's start a server so we can see our application running:

```bash
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



![](<../../.gitbook/assets/image (2).png>)



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
