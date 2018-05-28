# Relocating

The framework provides you with the `relocate()` method that you can use to relocate to other events thanks to the framework super type object, the grand daddy of all things ColdBox.

{% hint style="info" %}
Please see the [Super Type CFC Docs](http://apidocs.ortussolutions.com/coldbox/current) for further investigation of all the goodness of methods you have available.
{% endhint %}

```javascript
/**
* Relocate user browser requests to other events, URLs, or URIs.
*
* @event The name of the event to run, if not passed, then it will use the default event found in your configuration file
* @URL The full URL you would like to relocate to instead of an event: ex: URL='http://www.google.com'
* @URI The relative URI you would like to relocate to instead of an event: ex: URI='/mypath/awesome/here'
* @queryString The query string to append, if needed. If in SES mode it will be translated to convention name value pairs
* @persist What request collection keys to persist in flash ram
* @persistStruct A structure key-value pairs to persist in flash ram
* @addToken Wether to add the tokens or not. Default is false
* @ssl Whether to relocate in SSL or not
* @baseURL Use this baseURL instead of the index.cfm that is used by default. You can use this for ssl or any full base url you would like to use. Ex: https://mysite.com/index.cfm
* @postProcessExempt Do not fire the postProcess interceptors
* @statusCode The status code to use in the relocation
*/
void function relocate(
	event,
	URL,
	URI,
	queryString,
	persist,
	struct persistStruct,
	boolean addToken,
	boolean ssl,
	baseURL,
	boolean postProcessExempt,
	numeric statusCode
)
```

It is **extremely** important that you use this method when relocating instead of the native ColdFusion methods as it allows you to gracefully relocate to other events or external URIs. By graceful, we mean it does a lot more behind the scenes like making sure the flash scope is persisted, logging, post processing interceptions can occur and safe relocations.

So always remember that you relocate via `relocate()` and if I asked you: "Where in the world does event handlers get this method from?", you need to answer: "From the super typed inheritance".

```java
relocate( "home" );
relocate( event="shop", ssl=true );
relocate( event="user.view", queryString="id=#rc.id#" );
relocate( url="http://www.google.com" );
relocate( uri="/docs/index.html" )
```



