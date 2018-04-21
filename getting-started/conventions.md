# Conventions

The core conventions delineate the contract between ColdBox and you for file/directory locations and more. Below is a table of the core conventions:

## Directory/File Conventions

* **coldbox** - This is the ColdBox framework managed by CommandBox
* **config/Coldbox.cfc** - Your application configuration object
* **config/Router.cfc** - Your application URL Router
* **handlers** - This holds the app's controllers
* **layouts** - Your HTML layouts
* **models** - This holds your app's CFCs 
* **modules** - This holds the CommandBox tracked modules
* **modules\_app **- This holds your app's modules
* **views** - Your HTML views will go here

## Execution Conventions

| Convention | Required | Default Value | Description |
| --- | --- | --- | --- |
| Default Event | false | `main.index` | The default event to execute when no event is specified |
| Default Action | false | `index()` | The default action to execute in an event handler controller if none is specified |
| Default Layout | false | `layouts/Main.cfm` | The default system layout to use |

Let's get started with the [Directory Structure](https://github.com/ortus-docs/coldbox-docs/tree/913ae5524597b26261476dfd3506663b3a189bfa/the-basics/configuration/directory-structure.md)

## Directory Structure

Now that we have seen the [core conventions](conventions.md), we can layout our application. Most of the time we would recommend you use the packaged application templates in the ColdBox bundle download or by leveraging the CommandBox ColdBox creation commands.

To get started with a structure, go into CommandBox interactive shell and let's create a ColdBox application:

```bash
CommandBox>coldbox create app name=MyApp skeleton=AdvancedScript
```

This will create a Coldbox application with the following structure:

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/ApplicationTemplate.png)

> **Info**: Please note that the only required files/directories are `Application.cfc, index.cfm and handlers`. The rest are optional.

| File/Directory | Description |
| --- | --- |
| Application.cfc | Every ColdFusion application needs one. This includes the basic ColdBox bootstrap code inside of it. |
| box.json | The CommandBox package descriptor file |
| includes | Where all JavaScript, CSS and static assets go |
| index.cfm | The front controller that dispatches ColdBox requests |
| interceptors | An optional folder where you can store ColdBox Interceptors |
| tests | Yes, we have to do it. The location of your TestBox test harness |

## Application Templates

The official ColdBox application templates can be found in our github page: [https://github.com/coldbox-templates](https://github.com/coldbox-templates). Please note that CommandBox's `skeleton` argument can use any of the aliases below or any CommandBox endpoint. This means you can have your own templates.

| Template | Description |
| --- | --- |
| Advanced | The advanced template that includes every convention and optional configuration in tag format |
| AdvancedScript | The advanced template that includes every convention and optional configuration in script format |
| elixir | Advanced script template with ColdBox elixir support for asset pipelines |
| elixir-bower | Advanced script template with ColdBox elixir support for asset pipelines and bower support |
| elixir-vuewjs | Advanced script template with ColdBox elixir support for asset pipelines and VueJS integration |
| rest | A RESTFul services application |
| Simple | A fairly simple structure |
| SuperSimple | The basics |

