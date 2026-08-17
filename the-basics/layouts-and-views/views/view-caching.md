# View Caching

You can also pass in the caching arguments below and your view will be rendered once and then cached for further renderings. Every ColdBox application has two active cache regions: `default and template`. All view and event caching renderings go into the `template` cache.

| Argument               | Type    | Required | Default            | Description                                                                               |
| ---------------------- | ------- | -------- | ------------------ | ----------------------------------------------------------------------------------------- |
| cache                  | boolean | false    | false              | Cache the view to be rendered                                                             |
| cacheTimeout           | numeric | false    | (provider default) | The timeout in minutes or whatever the cache provider defines                             |
| cacheLastAccessTimeout | numeric | false    | (provider default) | The idle timeout in minutes or whatever the cache provider defines                        |
| cacheSuffix            | string  | false    | ---                | Adds a suffix key to the cached key. Used for providing uniqueness to the cacheable entry |

```javascript
component name="general"{

    function index(event,rc,prc){

        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();    
        // view with caching parameters
        event.setView(
            view="general/index",
            cache=true,
            cacheTimeout=60,
            cacheLastAccessTimeout=15,
            cacheSuffix=getfwLocale()
        );
    }

}
```

## Purging Views

So now that our views are cached, how do I purge them programmatically? Well, you need to talk to the `template` cache provider and use the clearing methods:

```javascript
// get a reference to the template cache
cache = cachebox.getCache( 'template' );
// or via shortcut notation
cache = getCache( "template" );
// or injection
property name="cache" inject="cachebox:template";
```

Then we can perform several operations on views:

* `clearView(string viewSnippet)`: Used to clear a view from the cache by using a snippet matched according to name + cache suffix.
* `clearMultiView(any viewSnippets)`: Clear using a list or array of view snippets.
* `clearAllViews([boolean async=true])` : Can clear ALL cached views in one shot and can be run asynchronously.

```javascript
cachebox.getCache( 'template' ).clearView('general/index');
cachebox.getCache( 'template' ).clearAllViews(async=true);
cachebox.getCache( 'template' ).clearMultiView('general/index','index','home');
```

## Disable View Caching

To turn off view caching for your entire application, set the `viewCaching` setting to false in your `config/Coldbox.cfc` config file.

```javascript
coldbox = {
// Activate view caching
viewCaching = false
}
```

## View Discovery Caching

`viewDiscoveryCaching` is a separate setting from `viewCaching` above - it's on by default and controls a different thing entirely. `viewCaching` caches a view's rendered **output**; `viewDiscoveryCaching` caches the **path resolution** work the Renderer does to locate a view/layout file on disk (module fallback checks, extension detection, etc) - pure overhead that never changes once a file exists at a given path.

```javascript
coldbox = {
    // Cache the filesystem lookups that locate view/layout files. Default: true
    viewDiscoveryCaching = true
}
```

{% hint style="info" %}
Turning this off does not disable `viewCaching` - the two settings are independent. You'd disable `viewDiscoveryCaching` only if you're dynamically adding view files to a running application and need every request to re-check the filesystem for them.
{% endhint %}
