# The Base Proxy Object

Here are some common methods of our ColdBox proxy object.  However, we encourage you to see the [API docs](http://apidocs.ortussolutions.com/coldbox/current) for that latest and greatest.

|Method|Description|
|--|--|
|announceInterception(state, data) |Processes a remote interception.|
|getCacheBox()	|Get a reference to [CacheBox](http://wiki.coldbox.org/wiki/CacheBox.cfm)|
|getCache(cacheName='default')	|Get a reference to a named cache provider|
|getController() |Returns the ColdBox controller instance |
|getInterceptor()	|Get a named interceptor |
|getLogBox()	|Get a reference to LogBox|
|getModel(name,dsl,initArguments)	|Get a [Wirebox](http://wiki.coldbox.org/wiki/Wirebox.cfm) model object|
|getWireBox()	|Get a reference to [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm)|
|process() |Processes a remote call that will execute a coldbox event and returns data/objects back. |
|loadColdBox()	|Gives you the ability to load any external coldbox application in the application scope. Great for remotely loading any coldbox application, it can be located anywhere.|
|getRemotingUtil()	| Return a utility class to manipulate output and buffer |


<small>
API Docs: http://apidocs.ortussolutions.com/coldbox/current
</small>

## Autowiring Proxies

You can use mixin injection for Proxy CFCs that extend the base proxy. To do this, add `autowire=true` to the component metadata. The CFML engine creates the CFC, but the base proxy's pseudo constructor will autowire itself.

```js
component autowire=true {
  property name='myService' inject='myService';
  
}
```