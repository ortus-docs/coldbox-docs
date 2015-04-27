# View Events

All rendered views have associated events that are announced whenever the view is rendered via ColdBox Interceptors. These are great ways for you to be able to intercept when views are rendered and transform them, append to them, or even remove content from them in a completely decoupled approach. The way to listen for events in ColdBox is to write Interceptors, which are essential simple CFC's that by convention have a method that is the same name as the event they would like to listen to. Each event has the capability to receive a structure of information wich you can alter, append or remove from. Once you write these Interceptors you can either register them in your Configuration File or programmatically.

|Event|Data|Description|
|--|--|--|
|preViewRender |<ul><li>view - The name of the view to render</li><li>cache - If the view will be cached</li><li>cacheTimeout - The cache timeout</li><li>cacheLastAccessTimeout - The idle timeout of the view</li><li>cacheSuffix - A suffix to append to the cacheable key name</li><li>module - The module name of the view if any</li><li>args - The arguments to pass into the view</li><li>collection - The collection this view will iterate on</li><li>collectionAs - The alias of the collection name</li><li>collectionStartRow - The start row of the collection iteration</li><li>collectionMaxRows - The max rows of the collection iteration</li><li>collectionDelim - The delimiter used for the collection iteration </li>|Executed before a view is about to be rendered|
|postViewRender |All of the data above plus:<ul><li>renderedView - The view contents that was rendered </li></ul>|Executed after a view was rendered|

> **Caution** You can disable the view events on a per-rendering basis by passing the prePostExempt argument as true when calling renderView() methods.

## Sample Interceptor

Here is a sample interceptor that trims any content before it is renderer:

```js
component{

	function postViewRender(event,interceptData){
		interceptData.renderedView = trim( interceptData.renderedView );
	}
}
```

Of course, I am pretty sure you will be more creative than that!
