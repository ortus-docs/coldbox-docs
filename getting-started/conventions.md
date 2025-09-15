---
description: Discover the major conventions of the ColdBox framework
---

# Conventions

The core conventions delineate the contract between ColdBox and **you** for file/directory locations and more. Below is a table of the core conventions:

## Directory/File Conventions

* **/config** - Where configuration files are stored
  * **/Coldbox.cfc** - Your application configuration object (_optional_ )
  * **/CacheBox.cfc** - Your application CacheBox configuration (_optional_ )
  * **/Router.cfc** - Your application URL Router (_optional_ )
  * **/Scheduler.cfc** - Your application global task scheduler (optional)
  * **/WireBox.cfc** - Your application WireBox Configuration (_optional_ )
* **/handlers** - This holds the app's event handlers (controller layer)
* **/includes** - For public assets, helpers and i18n resources
  * **/css** - This can hold your CSS (optional)
  * **/js** - This can hold your JavaScript (optional)
* **/layouts** - Your HTML layouts (view layer)
* **/models** - This holds your app's CFCs  (model layer)
* **/modules** - This holds the CommandBox tracked modules
* **/modules\_app** - This holds your app's modules
* **/tests** - Your test harness, including unit and integration testing
  * **/specs** - Where your test bundles go
* **/views** - Your HTML views will go here (view layer)

## Execution Conventions

ColdBox also has several execution conventions.  This means that we have a convention or a default for the event, action, and layout to be used if you do not tell it what to use:

<table data-header-hidden><thead><tr><th width="179">Convention</th><th width="199.33333333333331">Default Value</th><th>Description</th></tr></thead><tbody><tr><td><strong>Convention</strong></td><td><strong>Default Value</strong></td><td><strong>Description</strong></td></tr><tr><td>Default Event</td><td><code>main.index</code></td><td>The default event to execute when no event is specified</td></tr><tr><td>Default Action</td><td><code>index()</code></td><td>The default action to execute in an event handler controller if none is specified</td></tr><tr><td>Default Layout</td><td><code>layouts/Main.cfm</code></td><td>The default system layout to use</td></tr></tbody></table>
