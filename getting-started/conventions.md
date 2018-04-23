# Conventions

The core conventions delineate the contract between ColdBox and you for file/directory locations and more. Below is a table of the core conventions:

## Directory/File Conventions

* **config/Coldbox.cfc** - Your application configuration object \(_optional_\)
* **config/Router.cfc** - Your application URL Router \(_optional_\)
* **handlers** - This holds the app's controllers
* **layouts** - Your HTML layouts
* **models** - This holds your app's CFCs 
* **modules** - This holds the CommandBox tracked modules
* **modules\_app **- This holds your app's modules
* **views** - Your HTML views will go here

## Execution Conventions

| **Convention** | **Default Value** | **Description** |
| --- | --- | --- | --- |
| Default Event | `main.index` | The default event to execute when no event is specified |
| Default Action | `index()` | The default action to execute in an event handler controller if none is specified |
| Default Layout | `layouts/Main.cfm` | The default system layout to use |

