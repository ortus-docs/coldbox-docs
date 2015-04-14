# Layout Events

All rendered layouts have associated events that are announced whenever the layout is rendered. These are great ways for you to be able to intercept when layouts are rendered and transform them, append to them, or even remove content from them in a completely decoupled approach. The way to listen for events in ColdBox is to write Interceptors, which are essential simple CFC's that by convention have a method that is the same name as the event they would like to listen to. Each event has the capability to receive a structure of information wich you can alter, append or remove from. Once you write these Interceptors you can either register them in your Configuration File or programmatically.

|Event|Data|Description|
|--|--|--|
|preLayoutRender |<ul><li>layout - The name of the layout to render</li><li>view - The name of the view to render if any</li><li>module - The module name of the layout if any</li><li>args - The arguments to pass into the layout + view (if any)</li><li>viewModule - The name of the module the view will be rendered from </li></ul>|Executed before any layout is rendered|
|postLayoutRender |All of the data above plus: <ul><li>renderedLayout - The layout + view contents that was rendered </li></ul>|Executed after a layout was rendered|

> **Important** You can disable the layout events on a per-rendering basis by passing the prePostExempt argument as true when calling `renderLayout()` methods. 



