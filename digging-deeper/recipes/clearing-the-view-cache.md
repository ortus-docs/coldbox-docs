# Clearing the View Cache

## Introduction

If you use any of the view partial caching mechanisms in Coldbox either through `setView()` or`renderView()` you might be asking yourself:

> Is there an easy, programmatic way to remove a specific element from the view cache?

The answer is, of course! All view and event caching occurs in a cache provider called template and you can retrieve it like so from your handlers, layouts, views, plugins and interceptors:

```javascript
var cache = cachebox.getCache("template");
```

You can also use the WireBox injection DSL

```javascript
property name="cache" inject="cachebox:template">
```

## Clearing methods

There are a few methods that will help you clear views:

* `clearView(viewSnippet)` - Clear views with a snippet
* `clearAllViews(async)` - Clear all views
* `clearViewMulti(viewSnippets)` - Clear multiple view snippets with a list or array of snippets

```javascript
getCache( "template" ).clearView('home');
```

Very easy! Just send in what you need and it will be purged.

