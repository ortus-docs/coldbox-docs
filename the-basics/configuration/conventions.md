# Conventions

The core conventions delineate the contract between ColdBox and you for file/directory locations and more. Below is a table of the core conventions:

## Directory/File Conventions

| Convention | Required | Default Value | Description |
| --- | --- | --- | --- |
| Config File | false | `config/ColdBox.cfc` | The location of the application configuration file |
| Handler Controllers | true | `handlers` | Where all event handler controllers are located |
| Layouts | false | `layouts` | Where all layouts are located |
| Models | false | `models` | Where all model objects are located |
| Modules | false | `modules` | Where all modules are located that are tracked by CommandBox |
| Custom modules | false | `modules_app` | Where non-tracked custom modules are located |
| Views | false | `views` | Where all views are located |

## Execution Conventions

| Convention | Required | Default Value | Description |
| --- | --- | --- | --- |
| Default Event | false | `main.index` | The default event to execute when no event is specified |
| Default Action | false | `index()` | The default action to execute in an event handler controller if none is specified |
| Default Layout | false | `layouts/main.cfm` | The default system layout to use |

Let's get started with the [Directory Structure](directory-structure.md)

