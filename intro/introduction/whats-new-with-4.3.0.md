# What's New With 4.3.0

ColdBox 4.3.0 is a minor release that addresses several issues and introduces some enhancements. You can see below the release notes.

## Module Parent Settings

We have now introduced a standardized approach to defining/overriding module settings in your module and overrides via the parent application. You can now define a `moduleSettings` structure in your `config/ColdBox.cfc` which will hold all the override settings for any modules you have installed in your system.

Module developers can then create defaults for those settings in a modules's `ModuleConfig.cfc` via the `settings` structure.

```javascript
// myModule/ModuleConfig.cfc
component {
  function configure() {
    settings = {
      someSetting = "default",
      anotherSetting = "default"
    };
  }
}

// config/ColdBox.cfc
component {
  function configure() {
    moduleSettings = {
      myModule = {
        someSetting = "overridden" 
      }
    };
  }
}

// end result
{
  someSetting = "overridden",
  anotherSetting = "default"
}
```

## Global `invalidHTTPMethodHandler`

You can now define a global invalid HTTP method handler in your `coldbox` configuration structure:

```text
coldbox = {
  invalidHTTPMethodHandler = "main.invalidHTTP"
}
```

This will provide your application with error consistency when building RESTFul services.

## New `allowedMethods` annotation for handlers

Your event handler actions can now define their allowed HTTP methods of execution via the `allowedMethods` annotation:

```javascript
function index( event, rc, prc) allowedMethods="GET,POST"{
...
}
```

## New convention `modules_app`

With the introduction of CommandBox we can now have tracked modules and un-tracked modules in a ColdBox application. The default convention for modules called `modules` is now the default location of tracked CommandBox modules. The new convention `modules_app` is for your own custom un-tracked modules.

What is tracked? Modules that are tracked by CommandBox and usually not added to source control. CommandBox controls their installation, updating, etc.

## Interceptors get `rc` and `prc` references

All interceptor methods now receive a reference to `rc` and `prc` for convenience:

```text
function preProcess( event, rc, prc, interceptData, buffer )
```

## String Builders

Internal concatenation tools and interceptor response buffers have been migrated to Java String Builders for a more awesome performance updates.

## Binary HTTP Content

You can now receive and decode binary HTTP content when doing RESTFul services

## Models in Modules accept aliases now

Models in Modules can now have name aliases via the `alias` annotation or binder alias definitions.

## HTTP Method Spoofing

Although we have access to all these HTTP verbs, modern browsers still only support `GET` and `POST`. With ColdBox and HTTP Method Spoofing, you can take advantage of all the HTTP verbs in your web forms.

By convention, ColdBox will look for an `_method` field in the form scope. If one exists, the value of this field is used as the HTTP method instead of the method that was made. For instance, the following block of code would execute with the `DELETE` action instead of the `POST` action:

```markup
<cfoutput>
<form method="POST" action="#event.buildLink('posts/#prc.post.getId()#')#">
    <input type="hidden" name="_method" value="DELETE" />
    <button type="submit">Delete</button>
</form>
</cfoutput>
```

You can manually add these `_method` fields yourselves, or you can take advantage of ColdBox's HTML Helper.

```markup
<cfoutput>
#html.startForm( action = "posts.#prc.post.getId()#", method="DELETE" )#
    #html.submitButton( name = "Delete", class = "btn btn-danger" )#
#html.endForm()#
</cfoutput>
```

## Release Notes

### Bugs

* \[[COLDBOX-479](https://ortussolutions.atlassian.net/browse/COLDBOX-479)\] - onInvalidHTTPMethod not firing with SES Interceptor
* \[[COLDBOX-512](https://ortussolutions.atlassian.net/browse/COLDBOX-512)\] - ColboxProxy.cfc has reference to method tracer - which no longer exists in that component.
* \[[COLDBOX-515](https://ortussolutions.atlassian.net/browse/COLDBOX-515)\] - Exception handling broken for customErrorTemplate when application relative path used
* \[[COLDBOX-517](https://ortussolutions.atlassian.net/browse/COLDBOX-517)\] - CF mapping's for modules don't get created for proxy requests
* \[[COLDBOX-520](https://ortussolutions.atlassian.net/browse/COLDBOX-520)\] - Only remove the `scriptname` from the `pathinfo` if it is the first element in the `pathinfo`
* \[[COLDBOX-521](https://ortussolutions.atlassian.net/browse/COLDBOX-521)\] - afterAspectsLoad was missing from core
* \[[COLDBOX-527](https://ortussolutions.atlassian.net/browse/COLDBOX-527)\] - bug report view is not thread safe
* \[[COLDBOX-528](https://ortussolutions.atlassian.net/browse/COLDBOX-528)\] - Coldbox Event Cache discards the Content-Type when caching non renderdata results
* \[[COLDBOX-529](https://ortussolutions.atlassian.net/browse/COLDBOX-529)\] - Object in RC or PRC causes async interceptors to fail
* \[[COLDBOX-534](https://ortussolutions.atlassian.net/browse/COLDBOX-534)\] - getHTMLBaseURL returns with a double slash at the end,
* \[[COLDBOX-537](https://ortussolutions.atlassian.net/browse/COLDBOX-537)\] - ColdBox Cache Flash not discovering session/cookie as app has not loaded first
* \[[COLDBOX-539](https://ortussolutions.atlassian.net/browse/COLDBOX-539)\] - Private event actions are no longer executable \(regression\)

### New Features

* \[[COLDBOX-371](https://ortussolutions.atlassian.net/browse/COLDBOX-371)\] - Convention to override a module's settings in an application or parent module
* \[[COLDBOX-505](https://ortussolutions.atlassian.net/browse/COLDBOX-505)\] - New coldbox directive: invalidHTTPMethodHandler that will fire globally if an action is called with an invalid HTTP Method
* \[[COLDBOX-511](https://ortussolutions.atlassian.net/browse/COLDBOX-511)\] - New action annotation "allowedMethods" so you can allow inline annoation for allowed HTTP Verbs thanks to Nic Tunney
* \[[COLDBOX-522](https://ortussolutions.atlassian.net/browse/COLDBOX-522)\] - Add rc and prc available directly to custom error templates
* \[[COLDBOX-525](https://ortussolutions.atlassian.net/browse/COLDBOX-525)\] - HTTP Method Spoofing for Forms
* \[[COLDBOX-530](https://ortussolutions.atlassian.net/browse/COLDBOX-530)\] - Add 'modules\_app' as a default external location instead of manually adding it.
* \[[COLDBOX-540](https://ortussolutions.atlassian.net/browse/COLDBOX-540)\] - Interception points get reference to rc and prc now.
* \[[COLDBOX-541](https://ortussolutions.atlassian.net/browse/COLDBOX-541)\] - invokerasync was not passing the buffer

### Improvements

* \[[COLDBOX-502](https://ortussolutions.atlassian.net/browse/COLDBOX-502)\] - RequestBuffers now leverage string builders.
* \[[COLDBOX-513](https://ortussolutions.atlassian.net/browse/COLDBOX-513)\] - Calling processState from within an interceptor clears buffer
* \[[COLDBOX-531](https://ortussolutions.atlassian.net/browse/COLDBOX-531)\] - execute\(\) in BaseTestCase now uses the default event \(defined in config/ColdBox.cfc\) if / is passed in as the route
* \[[COLDBOX-532](https://ortussolutions.atlassian.net/browse/COLDBOX-532)\] - event.getHTTPContent\(\) doesn't work on binary encoding
* \[[COLDBOX-536](https://ortussolutions.atlassian.net/browse/COLDBOX-536)\] - Rendered output has ALWAYS a precedent empty line, delete it!
* \[[COLDBOX-538](https://ortussolutions.atlassian.net/browse/COLDBOX-538)\] - Alises in module models aren't picked up

