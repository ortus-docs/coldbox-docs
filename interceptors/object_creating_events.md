# Object Creating Events

|Interception Point|Intercept Structure|Description|
|--|--|--|
|afterHandlerCreation | <ul><li></li><li></li></ul>|This occurs whenever a handler is created|
|afterInstanceCreation|<ul><li>mapping - The object mapping</li><li>target - The target object</li><li>injector - The WireBox injector that produced the object</li></ul>|This occurs whenever a [wirebox](http://wiki.coldbox.org/wiki/Wirebox.cfm) object is created|
|afterPluginCreation |<ul><li>pluginPath - The path to the plugin</li><li>custom - A custom or core plugin</li><li>module - A module plugin</li><li>oPlugin - The plugin object</li></ul>|This occurs whenever a plugin object is created|

[WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) also announces several other events in the object creation life cycles, so please see [the WireBox events](http://wiki.coldbox.org/wiki/WireBox.cfm#WireBox_Event_Model)