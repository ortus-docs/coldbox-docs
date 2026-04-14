---
description: Compose ColdBox superpowers into any object via WireBox delegation
---

# Delegates

ColdBox ships with a rich set of **built-in delegates** that let you compose framework capabilities directly into any object — handlers, models, services, schedulers, interceptors — without extending a base class or writing boilerplate proxy methods.

{% hint style="info" %}
Delegates are a WireBox feature. If you are new to the concept, read the [WireBox Delegators](https://wirebox.ortusbooks.com/usage/wirebox-delegators) page first to understand how delegation works, the `delegates` annotation, prefixes/suffixes, and targeted method delegation.
{% endhint %}

## Two Namespaces

ColdBox registers delegates under two WireBox namespaces:

| Namespace         | Description                                                                          | Registered by               |
| ----------------- | ------------------------------------------------------------------------------------ | --------------------------- |
| `@coreDelegates`  | General-purpose utilities: async, datetime, environment, flow, JSON, strings, population | WireBox (always available)  |
| `@cbDelegates`    | ColdBox-specific: rendering, routing, settings, interceptors, app modes, file locators, population from request | ColdBox (when running inside ColdBox) |

## Quick Start

{% tabs %}
{% tab title="BoxLang" %}
```bx
// A model that can render views, read settings, and build links
class
    delegates="Rendering@cbDelegates,
               Settings@cbDelegates,
               Routable@cbDelegates" {

    function getWidget( required string name ) {
        var config = getSetting( "widgets" )
        return view( "widgets/#name#", { config : config } )
    }

    function getLink() {
        return buildLink( "home.index" )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
// A model that can render views, read settings, and build links
component
    delegates="Rendering@cbDelegates,
               Settings@cbDelegates,
               Routable@cbDelegates" {

    function getWidget( required string name ) {
        var config = getSetting( "widgets" )
        return view( "widgets/#name#", { config : config } )
    }

    function getLink() {
        return buildLink( "home.index" )
    }

}
```
{% endtab %}
{% endtabs %}

Or use individual `property` injections when you need prefixes/suffixes or targeted method delegation:

{% tabs %}
{% tab title="BoxLang" %}
```bx
class {

    property name="rendering" inject="Rendering@cbDelegates" delegate;
    property name="settings"  inject="Settings@cbDelegates"  delegate;
    property name="flow"      inject="Flow@coreDelegates"    delegate;

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component {

    property name="rendering" inject="Rendering@cbDelegates" delegate;
    property name="settings"  inject="Settings@cbDelegates"  delegate;
    property name="flow"      inject="Flow@coreDelegates"    delegate;

}
```
{% endtab %}
{% endtabs %}

## Available Delegates

### Core Delegates (`@coreDelegates`)

General-purpose utilities available in **any** WireBox-managed application.

| WireBox ID                   | Description                                                      |
| ---------------------------- | ---------------------------------------------------------------- |
| `Async@coreDelegates`        | Async/parallel programming via the ColdBox AsyncManager          |
| `DateTime@coreDelegates`     | Date and time utilities via DateTimeHelper                       |
| `Env@coreDelegates`          | Java system properties and OS environment variables              |
| `Flow@coreDelegates`         | Fluent flow-control methods (`when`, `unless`, `peek`, `throwIf`, etc.) |
| `JsonUtil@coreDelegates`     | JSON serialization utilities                                     |
| `Population@coreDelegates`   | Object population from structs, JSON, XML, and queries           |
| `StringUtil@coreDelegates`   | String manipulation: slugify, camelCase, snakeCase, pluralize, etc. |

➡ See [Core Delegates](core-delegates.md) for method references and examples.

### ColdBox Delegates (`@cbDelegates`)

ColdBox-specific delegates available when your application runs inside ColdBox.

| WireBox ID                  | Description                                                       |
| --------------------------- | ----------------------------------------------------------------- |
| `AppModes@cbDelegates`      | Check the current application mode (debug, development, production, testing) |
| `Interceptor@cbDelegates`   | Announce and listen to ColdBox interception events                |
| `Locators@cbDelegates`      | Locate files and directories within the ColdBox application       |
| `Population@cbDelegates`    | Populate objects from the request collection, JSON, XML, or query |
| `Rendering@cbDelegates`     | Render views, layouts, and external views                         |
| `Routable@cbDelegates`      | Build links, routes, and access base URL helpers                  |
| `Settings@cbDelegates`      | Read and write ColdBox and module settings                        |

➡ See [ColdBox Delegates](coldbox-delegates.md) for method references and examples.
