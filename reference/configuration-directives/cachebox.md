---
description: The cachebox configuration directive — how ColdBox wires CacheBox into your application.
icon: database
---

# CacheBox Directive

The `cachebox` structure is based on the [CacheBox declaration DSL](https://cachebox.ortusbooks.com/configuration/cachebox-configuration/cachebox-dsl/), and it allows you to customize the caches in your application. Below are the main keys you can fill out in a ColdBox app.

📖 **Cache providers, eviction policies, and the full DSL live in the CacheBox book:** [cachebox.ortusbooks.com](https://cachebox.ortusbooks.com) — including the [ColdBox integration details](https://cachebox.ortusbooks.com/configuration/cachebox-configuration/coldbox-configuration).

```javascript
//cachebox configuration
cachebox = {
    // Location of the configuration class for CacheBox, bx or cfc
    configFile = "config/CacheBox.bx",
    // Scope registration for CacheBox
    scopeRegistration = {enabled=true,scope=application,key=cacheBox},
    // Default Cache Configuration
    defaultCache  = "views",
    // Caches Configuration
    caches      = {}
};
```

> **Info** : We would recommend you create a `config/CacheBox.bx` (or `.cfc` for CFML) and put all your caching configuration there instead of in the main ColdBox configuration file. This will give you further portability and decoupling.

## ConfigFile

An absolute or relative path to the CacheBox configuration class or XML file to use instead of declaring the rest of the keys in this structure. So if you do not define a cacheBox structure, the framework will look for the default value: `config/CacheBox.bx` (or `.cfc` for CFML) and it will load it if found. If not found, it falls back to the framework's shipped default configuration at `coldbox.system.web.config.CacheBox` — that is, `/coldbox/system/web/config/CacheBox.cfc`.

## ScopeRegistration

A structure that enables scope registration of the CacheBox factory in either server, cluster, application or session scope.

## DefaultCache

The configuration of the default cache which will have an implicit name of default which is a reserved cache name. It also has a default provider of CacheBox which cannot be changed.

## Caches

A structure where you can create more named caches for usage in your CacheBox factory.
