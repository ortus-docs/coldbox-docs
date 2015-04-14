# Viewlets (Portable Events)

The final chapter in this guide is to explain what we consider as a ColdBox viewlet or portable events. A viewlet is a self sufficient view or a widget that can live on its own, its data is pre-fetched and can just be renderer anywhere in your system. What in the world is this? Well, imagine a portal, in which each section of the portal is self-sufficient, with controls and data. You don't want to call all the handlers for this data for every single piece of content. It's not efficient, you need to create a separation. Well, a viewlet is such a separation that provides you with the ability to create sustainable events or viewlets. So how do we achieve this?

1. You will use the method runEvent() anywhere you want a viewlet to be displayed, usually in a layout or another view. This calls an internal event that will be in charge to prepare and render the viewlet.
2. Create the portable event for the viewlet like any other normal event with one exception.
3. The event will return a rendered view or content

Layout Code (Where I place my viewlet call)

```js
<div id="leftbar">
#runEvent(event='viewlets.userinfo',prepostExempt=true)#
</div>
```

This code just renders out the results of a `runEvent()` method call. I would suggest you look at [the API docs](http://apidocs.coldbox.org/) to discover all paramters to the `runEvent()` method call. The prePostExempt argument makes the execution of the event faster as it skips any preEvent/postEvent interception calls.

> **Info** To learn more about the `runEvent()` method, please refer to the online API Docs

Event Code (viewlets.userinfo)

```js
function userinfo(event,rc,prc){

	// place data in prc and prefix it to avoid collisions
	prc.userinfo_qData = userService.getUserInfo();

	// render out content 
	return renderView("viewlets/userinfo");
}
```
