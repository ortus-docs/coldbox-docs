# What's New With 4.0.0

## Introduction

ColdBox 4 is a major release in our ColdBox Platform series and includes
a new revamped MVC core and all extra functionality has been refactored
into modules. We have pushed the modular architecture to 1st class
citizen even in the core itself. There are several [compatibility
updates](upgrading_to_coldbox_400.md) that you must do in order to upgrade your ColdBox 3.X
applications to CodlBox 4 standards. You will notice that the source and
download of ColdBox 4 has been reduced by almost 75% in size. This is
now due to our modular approach were functionality can just be brought
in dynamically. How you might say?

### CommandBox

[CommandBox](http://www.ortussolutions.com/products/commandbox), our new ColdFusion (CFML) command line interface,
package manager and REPL. You can now use CommandBox to install
dependencies, modules and even ColdBox itself all from our centralized
code repository: [ForgeBox](http://www.coldbox.org/forgebox). To install ColdBox 4 Bleeding Edge
you can just type:

```bash
box install coldbox-be
```

You can even use CommandBox to generate ColdBox applications, modules,
handlers, etc. It has a plethora of commands to get you started in a
fantastic ColdBox Adventure:

```bash
// Get help on all the ColdBox commands
coldbox help
```

```bash
// create a ColdBox app with TestBox support
coldbox create app MyFirstApp --installTestBox
```

## Internal Library Updates
ColdBox is composed of three internal libraries: WireBox (DI & AOP), CacheBox (Caching), LogBox (Logging). Below you can find what's new with this release for each library:

- [WireBox 2.0.0](whats_new/wirebox_200.md)
- [CacheBox 2.0.0](whats_new/cachebox_200.md)
- [LogBox 2.0.0](whats_new/logbox_200.md)

Major Updates
-------------

### Performance Updates

The core has been completely revamped by removing ColdFusion 7/8 code,
decoupled from many features that are now available as modules and
rewrites to pure `cfscript` syntax. The end result is the fastes ColdBox
release since our 1.0.0 days. In our initial vanilla load tests, normal
requests would take around 4-6ms to execute.

### Model called Models

The convention has been updated so it matches the other convetions. You
now must create a **models** folder instead of a **model** folder.

### onInvalidHTTPMethod Handler Convention

We have created a new action convention in all your handlers called
`onInvalidHTTPMethod` which will be called for you if a request is
trying to execute an action in your handler without the right HTTP Verb.
It will be then your job to determine what to do next:

```javascript
function onInvalidHTTPMethod( faultAction, event, rc, prc ){
    return "Yep, onInvalidHTTPMethod works!";
}
```

### HTTP Content Auto Marshalling

The `getHTTPContent` method on the Request Context now takes in two
boolean arguments:

1.  **json**
2.  **xml**

If set, ColdBox will auto-marshall the HTTP Body content from JSON or
XML to native ColdFusion data types.

```javascript
myStruct  = event.getHTTPContent( json=true );
xmlObject = event.getHTTPContent( xml=true );
```

### SES regex route placeholders

You can now define a-la-carte regex matching on route placeholders. If
the route matches with that regex in the placeholder the value will now
be stored in the RC as well:

```javascript
addRoute( pattern = 'format/:extension-regex:(xml|json)' );
```

### New Global View Helper

You now have a new ColdBox core setting `viewsHelper` which is a
template that will be injected and binded to any layout/view that is
rendered. This means that finally you have a template that can be
globally available to any view/layout in your system.

```javascript
coldbox = {
    viewsHelper = "includes/helpers/ViewHelper.cfm" 
};
```

### Application Template Java Support

All the new ColdBox application templates have been updated to include a
folder called `lib` inside that is automatically wired for you to
class load Java classes and jars. All you have to do is drop any jar or
class file in the this folder and it will be available via
`createobject(“java”)` anywhere in your app.

### New Core Modules

All internal plugins and lots of functionality has been refactored out
of the ColdBox Core and into standalone Modules. All of them are in
[ForgeBox](http://www.coldbox.org/forgebox) and have their own Git repositories. Also, all of them are
installable via [CommandBox](http://www.ortussolutions.com/products/commandbox); Our ColdFusion (CFML) CLI, and Package
Manager. This has reduced the ColdBox Core by over **75%** in source size
and complexity. Not only that, but it allows us to be able to release patches and updates for feature functionality without releasing an entire framework release, but just 1 or more modules.

-   ColdBox Debugger

```bash
box install cbdebugger
```

-   Storages

```bash
box install cbstorages
```

-   Feeds

```bash
box install cbfeeds
```

-   Commons

```bash
box install cbcommons
```

-   i18n

```bash
box install cbi18n
```

-   ORM

```bash
box install cborm
```

-   ioc

```bash
box install cbioc
```

-   JavaLoader

```bash
box install cbjavaloader
```

-   AntiSamy

```bash
box install cbantisamy
```

-   MailServies

```bash
box install cbmailservices
```

-   MessageBox

```bash
box install cbmessagebox
```

-   Soap

```bash
box install cbsoap
```

-   Security

```bash
box install cbsecurity
```

-   Validation

```bash
box install cbvalidation
```

### New Anti-Forgery Module

We have create a nice anti-forgery module called **csrf** which can be
found in [ForgeBox](http://www.coldbox.org/forgebox/view/cbcsrf) and in GitHub: <https://github.com/ColdBox/cbox-csrf>. This
module will enhance your ColdBox applications with Anti-Cross Site
Request Forgery capabilities. You can also install it via CommandBox

```bash
box install csrf
```

### ORM Module Updates

As you know by now, all the ColdBox ORM features are available as a
module that can be installed in your application via CommandBox or
downloaded separately. We have done several updates to the ORM
extensions like:

-   Railo multi-datasource support
-   Expanded `createAlias()` method to allow for a **criteria** argument
    which leverages hibernate's ability to do a where statement on a
    join
-   Script updates

### New System Renderer

The ColdBox Renderer plugin has been removed and it is now part of the
core as the system renderer (`coldbox.system.web.Renderer`).

-   It has been migrated to full script and optimized for ColdFusion 9+
    syntax
-   We have also created a new DSL to inject it via WireBox: `coldbox:renderer`
-   You can also add mixins or alter its behavior by talking to its
    WireBox mapping (`Renderer@coldbox`)
-   All handlers/interceptors/views/layouts have access to the renderer
    by calling the `getRenderer()` method in the super type
-   The main ColdBox controller has a new method called `getRenderer()` to retrieve the system renderer

### New Error Template

By default, we are now not showing any exceptions in the ColdBox default
error template. You now have to specify the full exception bug template
if you would like to see the exceptions via the **CustomErrorTemplate**:

``` {.coldfusion}
coldbox = {
    customErrorTemplate = "/coldbox/system/includes/BugReport.cfm"
};
```

This is to be secure by default. The template used is
**/coldbox/system/includes/BugReport-Public.cfm**

### Remote Proxies Autowired

You've always been able to get models in a remote proxy (web-accessible
CFC that extends coldbox.system.remote.ColdBoxProxy) using the
getModel() method. Now you can also autowire your remote proxies using
cfproperties just like you do in handlers and WireBox-managed models.

**/remote/myProxy.cfc**

``` {.coldfusion}
component extends="coldbox.system.remote.ColdBoxProxy" {
    property name='myService' inject='myService';

    remote function doRemote() {
        variables.myService.doSomething();
    }
}
```

Note, the autowiring only works for web-accessible remote proxies being
directly invoked. You can extend the ColdBoxProxy by another
manually-created CFC (such as an ORM eventHandler) but it won't be
autowired.

### Handlers Are Now Singletons

In pre-4.0.0 applications all event handlers were cached in CacheBox
with specific timeouts. We have found that this just created extra noise
and complexity for handler CFCs. So now all event handlers will be
cached as singletons be default (unless specified in the Col

</h3>