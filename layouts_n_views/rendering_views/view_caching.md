# View Caching

You can also pass in the caching arguments below and your view will be rendered once and then cached for further renderings. Every ColdBox application has two active cache regions: `default and template`. All view and event caching renderings go into the `template` cache.

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|cache|boolean|false|false|Cache the view to be rendered|
|cacheTimeout|numeric|false|(provider default)|The timeout in minutes or whatever the cache provider defines|
|cacheLastAccessTimeout |numeric|false|(provider default)|The idle timeout in minutes or whatever the cache provider defines|
|cacheSuffix|string|false|---|Adds a suffix key to the cached key. Used for providing uniqueness to the cacheable entry|

```js
component name="general"{

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getModel('MyService').getData();	
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

```js
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

```js
cachebox.getCache( 'template' ).clearView('general/index');
cachebox.getCache( 'template' ).clearAllViews(async=true);
cachebox.getCache( 'template' ).clearMultiView('general/index','index','home');
```

