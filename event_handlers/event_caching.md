# Event Caching

Event caching is extremely useful and easy to use. All you need to do is add several metadata arguments to the action methods and the framework will cache the output of the event in the template cache provider in CacheBox. In other words, the event executes and produces output that the framework then caches. So the subsequent calls do not do any processing, but just output the content. For example, you have an event called blog.showEntry. This event executes, gets an entry from the database and sets a view to be rendered. The framework then renders the view and if event caching is turned on for this event, the framework will cache the HTML produced. So the next incoming show entry event will just spit out the cached html. Important to note also, that any combination of URL/FORM parameters on an event will produce a unique cacheable key. So event=blog.showEntry&id=1 & event=blog.showEntry&id=2 are two different cacheable events.

Almost all of the entire life cycle is skipped, the content is just delivered. Below you can see the life cycle of both cached and normal events:

![](../images/EventCachingFlow)