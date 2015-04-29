# Module Events

The module service also announces several events or interception points as you saw from the life cycle diagrams. Below are the events announced:

## preModuleLoad
Fired before a module is about to be activated.

**Data:**
* `moduleLocation` - The location of the loaded module
* `moduleName` - The name of the module

## postModuleLoad
Fired after a module has been successfully activated

**Data:**
moduleLocation - The location of the loaded module
moduleName - The name of the module
moduleConfig - The module configuration structure

## preModuleLoad
Fired before a module is about to be unloaded

**Data:**
* `moduleName` - The name of the module

## preModuleLoad
Fired after a module has been unloaded

**Data:**
* `moduleName` - The name of the module