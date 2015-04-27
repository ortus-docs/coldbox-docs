# Viewlets (Portable Events)

The final chapter in this guide is to explain what we consider as a ColdBox viewlet or portable events. A viewlet is a self sufficient view or a widget that can live on its own, its data is pre-fetched and can just be renderer anywhere in your system. 

What in the world is this? Well, imagine a portal, in which each section of the portal is self-sufficient, with controls and data. You don't want to call all the handlers for this data for every single piece of content. It's not efficient, you need to create a separation. Well, a viewlet is such a separation that provides you with the ability to create sustainable events or viewlets. So how do we achieve this?

1. You will use the method `runEvent()` anywhere you want a viewlet to be displayed or the content rendered. This calls an internal event that will be in charge to prepare and render the viewlet.
2. Create the portable event but make sure it returns the produced content.

**Layout Code (Where I place my viewlet call)**

```js
<div id="leftbar">
#runEvent(event='viewlets.userinfo',prepostExempt=true)#
</div>
```

This code just renders out the results of a `runEvent()` method call. I would suggest you look at [the API docs](http://apidocs.ortussolutions.com/coldbox/current) to discover all arguments to the `runEvent()` method call. The `prePostExempt` argument makes the execution of the event faster as it skips any preEvent/postEvent interception calls.


Event Code (viewlets.userinfo)

```js
function userinfo(event,rc,prc){

	// place data in prc and prefix it to avoid collisions
	prc.userinfo_qData = userService.getUserInfo();

	// render out content 
	return renderView("viewlets/userinfo");
}
```
This handler code is where the magic happens. I talk to a service layer and place some data on the private request collection my viewlet will use. I then return the results of a `renderView()` call that will render out the exact viewlet I want. You can be more creative and do things like:

* render a layout + view combo
* render data
* return your own custom strings
* etc

> **Important** We would suggest you namespace or prefix your private request collection variables for viewlets in order to avoid collisions from multiple viewlet events in the same execution thread. 

Viewlet Code (viewlets/userinfo.cfm) 

```js
<cfoutput>
	<div>User Info Panel</div>
	<div>Username: #prc.userinfo_qData.username#</div>
	<div>Last Login: #prc.userinfo_qData.lastLogin#</div>
</cfoutput>
```

My view is a normal standard view, it doesn't even know it is a viewlet, remember, views are DUMB!