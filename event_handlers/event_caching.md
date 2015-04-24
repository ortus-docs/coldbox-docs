# Event Caching

Event caching is extremely useful and easy to use. All you need to do is add several metadata arguments to the action methods and the framework will cache the output of the event in the **template** cache provider in CacheBox. In other words, the event executes and produces output that the framework then caches. So the subsequent calls do not do any processing, but just output the content. For example, you have an event called `blog.showEntry`. This event executes, gets an entry from the database and sets a view to be rendered. The framework then renders the view and if event caching is turned on for this event, the framework will cache the HTML produced. So the next incoming show entry event will just spit out the cached html. Important to note also, that any combination of URL/FORM parameters on an event will produce a unique cacheable key. So `event=blog.showEntry&id=1` & `event=blog.showEntry&id=2` are two different cacheable events.

Almost all of the entire life cycle is skipped, the content is just delivered. Below you can see the life cycle of both cached and normal events:

![](../images/EventCachingFlow.jpg)

## Enabling Event Caching

To enable event caching, you will need to set a setting in your `ColdBox.cfc` called `EventCaching` to `true`. This tels ColdBox to be ready to read action metadata for cacheable events.
 
 ```js
 coldbox.eventCaching = true;
 ```
 
 > **Important** Enabling event caching does not mean that ALL events will be cached. It just means that you enable this feature. 
 
 ##Setting Up Actions For Caching
 
 The way to set up an event for caching is on the function declaration with the following extra attributes (annotations):
 
|Attribute|Type|Description|
|--|--|--|
|cache|boolean|A true or false will let the framework know whether to cache this event or not. The default is FALSE. So setting to false makes no sense|
|cachetimeout|numeric|The timeout of the event's output in minutes. This is an optional attribute and if it is not used, the framework defaults to the default object timeout in the cache settings. You can place a 0 in order to tell the framework to cache the event's output for the entire application timeout controlled by coldfusion, NOT GOOD. Always set a decent timeout for content. |
|cacheLastAccesstimeout |numeric|The last access timeout of the event's output in minutes. This is an optional attribute and if it is not used, the framework defaults to the default last access object timeout in the cache settings. This tells the framework that if the object has not been accessed in X amount of minutes, then purge it.|


> **Important** Please be aware that you should not cache output with 0 timeouts (forever). Always use a timeout. 

```js
// In Script
function showEntry(event,rc,prc) cache="true" cacheTimeout="30" cacheLastAccessTimeout="15"{
	//get Entry
	prc.entry = getEntryService().getEntry(event.getValue('entryID',0));
		
	//set view
	event.setView('blog/showEntry');
}
```

> **Alert** DO NOT cache events as unlimited timeouts. Also, all events can have an unlimited amount of permutations, so make sure they expire and you purge them constantly. Every event + URL/FORM variable combination will produce a new cacheable entry. 


## Storage

All event and view caching are stored in a named cache called `template` which all ColdBox applications have by default. You can open or create a new [CacheBox](http://cachebox.ortusbooks.com) configuration object and decide where the storage is, timeouts, providers, etc. You have complete control of how event and view caching is stored.

## Purging
We also have a great way to purge these events programmatically via our cache provider interface.

```js
templateCache = cachebox.getCache( "template" );
```

Methods for eent purging:

* `clearEvent(string eventSnippet, string querystring="")`: Clears all the event permutations from the cache according to snippet and querystring. Be careful when using incomplete event name with query strings as partial event names are not guaranteed to match with query string permutations
* `clearEventMulti(eventsnippets,string querystring="")`: Clears all the event permutations from the cache according to the list of snippets and querystrings. Be careful when using incomplete event name with query strings as partial event names are not guaranteed to match with query string permutations
* `clearAllEvents([boolean async=true])` : Can clear ALL cached events in one shot and can be run asynchronously.

```js
//Trigger to purge all Events
getCache( "template" ).clearAllEvents();

//Trigger to purge all events synchronously
getCache( "template" ).clearAllEvents(async=false);

//Purge all events from the blog handler
getCache( "template" ).clearEvent('blog');

//Purge all permutations of the blog.dspBlog event
getCache( "template" ).clearEvent('blog.dspBlog');

//Purge the blog.dspBlog event with entry of 12345
getCache( "template" ).clearEvent('blog.dspBlog','id=12345')
```

### this.event_cache_suffix

Do you remember this feature property? This property is great for adding your own dynamic suffixes when using event caching. All you need to do is create a public property called `EVENT_CACHE_SUFFIX` and populate it with something you want. Then the event caching mechanisms will automatically append the suffix and thus create event caching using this suffix for the entire handler.

`this.EVENT_CACHE_SUFFIX = "My Suffix";`

> **Info** This suffix will be appended to ALL events that are marked for caching within the handler in question ONLY. 

## OnRequestCapture - Influence Cache Keys

We have provided an interception point in ColdBox that allows you to add variables into the request collection before a snapshot is made so you can influence the cache key of a cacheable event. What this means is that you can use it to mix in variables into the request collection that can make this events unique for a user, a specific language, country, etc. This is a great way to leverage event caching on multi-lingual or session based sites.

```js
component{
	
	onRequestCapture(event,interceptData){
		var rc = event.getCollection();

		// Add user's locale to the request collection to influenze event caching
		rc._user_locale = getFWLocale();
	}

}
```

With the simple example above, all your event caching permutations will be addded the user's locale and thus create entries for different languages.

## Mentoring

[CacheBox](http://cachebox.ortusbooks.com) has an intuitive and powerful monitor that can be used via the ColdBox Debugger Module. From the monitor you can purge, expire and view cache elements, etc.

```
box install cbdebugger
```


![](../images/cachemonitor.jpg)

