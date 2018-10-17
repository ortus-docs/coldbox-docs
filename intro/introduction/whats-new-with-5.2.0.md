# What's New With 5.2.0

ColdBox 5.2.0 is a minor version update with lots of fixes, improvements and some new features not only to the ColdBox core but to LogBox and WireBox alike.  Below are the major areas of improvement and the full release notes. To update your ColdBox installation just leverage CommandBox: `update coldbox`

## ColdBox

The major areas of improvement for the ColdBox core where on tons of fixes, however there are some nice new features discussed below.

### Bugs

* \[[COLDBOX-597](https://ortussolutions.atlassian.net/browse/COLDBOX-597)\] - Function `addAsset` generate wrong link if asset path contains ".js"
* \[[COLDBOX-668](https://ortussolutions.atlassian.net/browse/COLDBOX-668)\] - Make implicit views case sensitive by default for linux systems
* \[[COLDBOX-677](https://ortussolutions.atlassian.net/browse/COLDBOX-677)\] - HTML HELPER - Fix a requirement for `columnName` if column is defined
* \[[COLDBOX-696](https://ortussolutions.atlassian.net/browse/COLDBOX-696)\] - Passing headers to \``request`\` breaks RoutingService when in test mode
* \[[COLDBOX-724](https://ortussolutions.atlassian.net/browse/COLDBOX-724)\] - Add `ENV` overload in `detectEnvironment()` via ENVIRONMENT system setting/property
* \[[COLDBOX-726](https://ortussolutions.atlassian.net/browse/COLDBOX-726)\] - setNextEvent does not exist in coldbox.system.web.Controller
* \[[COLDBOX-727](https://ortussolutions.atlassian.net/browse/COLDBOX-727)\] - fail fast strong typed to boolean, update to allow closures
* \[[COLDBOX-729](https://ortussolutions.atlassian.net/browse/COLDBOX-729)\] - getRenderData\(\) in base test case was not looking at the right request collection
* \[[COLDBOX-732](https://ortussolutions.atlassian.net/browse/COLDBOX-732)\] - Fail fast can't be turned off for original behavior
* \[[COLDBOX-736](https://ortussolutions.atlassian.net/browse/COLDBOX-736)\] - Module mappings disappear when not unloading ColdBox in base test case
* \[[COLDBOX-738](https://ortussolutions.atlassian.net/browse/COLDBOX-738)\] - Clear not working on string builders: Use setLength\(0\) since clear is not a method on StringBuilder
* \[[COLDBOX-740](https://ortussolutions.atlassian.net/browse/COLDBOX-740)\] - When using group\(\) operations with a handler and no explicit handler routing call added, route never registered the handler

### New Features

* \[[COLDBOX-722](https://ortussolutions.atlassian.net/browse/COLDBOX-722)\] - New global directive: `autoMapModels` which if **true**, it will map all root models just like modules do.

This will allow root **models** to behave like modules where all models are registered automatically for you but with no namespace.

* \[[COLDBOX-737](https://ortussolutions.atlassian.net/browse/COLDBOX-737)\] - `toAction()` terminator is missing from the new router DSL

This terminator was missing from the new Routing DSL.  This will allow you to build up routes that terminate at an action.

* \[[COLDBOX-720](https://ortussolutions.atlassian.net/browse/COLDBOX-720)\] - Register `config/Router.cfc` as an interceptor

The main application router and **ALL** module routers are now also interceptors.

### Improvements

* \[[COLDBOX-730](https://ortussolutions.atlassian.net/browse/COLDBOX-730)\] - Implicitly pass args from `renderLayout()` into the rendered views
* \[[COLDBOX-739](https://ortussolutions.atlassian.net/browse/COLDBOX-739)\] - List modules which have already been processed when a module cannot be activated to help with debugging



## LogBox

### Bugs

* \[[LOGBOX-29](https://ortussolutions.atlassian.net/browse/LOGBOX-29)\] - when using async option on FileAppender, nothing logs, well now it does!

### New Features

* \[[LOGBOX-31](https://ortussolutions.atlassian.net/browse/LOGBOX-31)\] - Add `defaultValue` arguments to `getProperty()` methods on abstract appenders

### Improvements

* \[[LOGBOX-30](https://ortussolutions.atlassian.net/browse/LOGBOX-30)\] - Leave off text `"ExtraInfo: "` from console appender if empty string

## WireBox

### Bugs

* \[[WIREBOX-76](https://ortussolutions.atlassian.net/browse/WIREBOX-76)\] - Virtual inheritance not injecting generic **getters** and **setters** correctly on target objects
* \[[WIREBOX-77](https://ortussolutions.atlassian.net/browse/WIREBOX-77)\] - Virtual inheritance not inheriting `init` from super class

### Improvements

* \[[WIREBOX-51](https://ortussolutions.atlassian.net/browse/WIREBOX-51)\] - Add method to binder to override alias of current mapping, by passing the current mapping to the influencer closure
* \[[WIREBOX-75](https://ortussolutions.atlassian.net/browse/WIREBOX-75)\] - Don't exclude path in parent mapping destinations
* \[[WIREBOX-78](https://ortussolutions.atlassian.net/browse/WIREBOX-78)\] - Simplify error message for missing dependency to be human readable

