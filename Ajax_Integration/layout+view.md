# Layout+View

With a normal Ajax GET or POST you can also just return a normal ColdBox layout + view combination, which is a normal ColdBox Event.

```js
function loginForm(){
	// setup here.
	event.setView(view="security/loginForm");
}
```

This event would render out the *security/loginForm.cfm* template in the default layout back to the calling user, whether Ajax or not. However, sometimes we might just want the view to be returned with no layout at all:

```js
function loginForm(){
	// setup here.
	event.setView(view="security/loginForm", noLayout=true);
}
```

This would just render out the set view back to the caller. Sometimes it is also best practice to create an Ajax layout where you can control how HTML ajax requests are rendered back:

```js
function loginForm(){
	// setup here.
	event.setView(view="security/loginForm", layout="Ajax");
}
```

#### Layout Code:
```js
<---  Remove CF Output --->
<cfsetting showdebugoutput="false">
<---  Remove the COldBox Debugger --->
<cfset event.showdebugpanel("false")>
<---  Render a messagebox, just for kicks --->
<cfset WriteOutput(getPlugin("messageBox").renderit())>
<---  Render the set View --->
<cfset WriteOutput(renderView())>
```
The first thing we do is remove the ColdFusion debuggin, if available. Then we use the special magic function in the event object to disable the coldbox debugger: event.showdebugpanel( boolean ). Then we just output a messagebox if available and then we output the view to be rendered out which is set by any event handler. That's it. The interesting methods here are that I tell ColdBox to remove the debugger panel if I am in debugging mode. This layout is then used to render any view that is brought via Ajax. This opens a world of ideas on how it can be so flexible for your view renderings.