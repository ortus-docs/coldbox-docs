# Object Creating Events


|Interception Point|Intercept Structure|Description|
|--|--|--|
|afterHandlerCreation | <ul><li>handlerPath -  The handler path</li><li>oHandler - The target handler object</li></ul>|This occurs whenever a handler is created|
|afterInstanceCreation|<ul><li>mapping - The object mapping</li><li>target - The target object</li><li>injector - The WireBox injector that produced the object</li></ul>|This occurs whenever a [wirebox](http://wiki.coldbox.org/wiki/Wirebox.cfm) object is created|

[WireBox](http://wirebox.ortusbooks.com/content/wirebox_event_model/index.html) also announces several other events in the object creation life cycles, so please see [the WireBox events](http://wirebox.ortusbooks.com/content/wirebox_event_model/index.html)
