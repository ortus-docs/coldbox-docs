# Conventions

The core conventions delineate the contract between ColdBox and you for file/directory locations and more. Below is a table of the core conventions:

## Directory/File Conventions

* **config/Coldbox.cfc** - Your application configuration object \(_optional_ \)
* **config/Router.cfc** - Your application URL Router \(_optional_ \)
* **config/CacheBox.cfc** - Your application CacheBox configuration \(_optional_ \)
* **config/WireBox.cfc** - Your application WireBox Configuration \(_optional_ \)
* **handlers** - This holds the app's event handlers \(controller layer\)
* **layouts** - Your HTML layouts \(view layer\)
* **models** - This holds your app's CFCs  \(model layer\)
* **modules** - This holds the CommandBox tracked modules
* **modules\_app** - This holds your app's modules
* **views** - Your HTML views will go here \(view layer\)

## Execution Conventions

ColdBox also has several execution conventions.  This means that we have a convention or a default for the event, action and layout to be used if you do not tell it what to use:

| **Convention** | **Default Value** | **Description** |
| :--- | :--- | :--- |
| Default Event | `main.index` | The default event to execute when no event is specified |
| Default Action | `index()` | The default action to execute in an event handler controller if none is specified |
| Default Layout | `layouts/Main.cfm` | The default system layout to use |

