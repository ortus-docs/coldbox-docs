# Relocating

The framework provides you with a method that you can use to relocate to other events thanks to the framework super type object, the grand daddy of all things ColdBox.

```javascript
/**
* Relocate the user to another location
* @event.hint The name of the event to run, if not passed, then it will use the default event found in your configuration file
* @URL.hint The full URL you would like to relocate to instead of an event: ex: URL='http://www.google.com'
* @URI.hint The relative URI you would like to relocate to instead of an event: ex: URI='/mypath/awesome/here'
* @queryString.hint The query string to append, if needed. If in SES mode it will be translated to convention name value pairs
* @persist.hint What request collection keys to persist in flash ram
* @persistStruct.hint A structure key-value pairs to persist in flash ram
* @addToken.hint Wether to add the tokens or not. Default is false
* @ssl.hint Whether to relocate in SSL or not
* @baseURL.hint Use this baseURL instead of the index.cfm that is used by default. You can use this for ssl or any full base url you would like to use. Ex: https://mysite.com/index.cfm
* @postProcessExempt.hint Do not fire the postProcess interceptors
* @statusCode.hint The status code to use in the relocation
*/
void function setNextEvent
```

It is **extremely** important that you use this method when relocating instead of the native ColdFusion methods as it allows you to gracefully relocate to other events or external URIs. By graceful, we mean it does a lot more behind the scenes like making sure the flash scope is persisted, logging, post processing interceptions can occurr and safe relocations. So always remember that you relocate via `setNextEvent` and if I asked you: "Where in the world does event handlers get this method from?", you need to answer: "From the super typed inheritance".

```text
setNextEvent( "home" );
setNextEvent( event="shop", ssl=true );
setNextEvent( event="user.view", queryString="id=#rc.id#" );
setNextEvent( url="http://www.google.com" );
setNextEvent( uri="/docs/index.html" )
```

