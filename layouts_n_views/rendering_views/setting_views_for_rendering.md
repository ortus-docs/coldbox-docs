# Setting Views For Rendering

Usually, event handlers are the objects in charge of setting views for rendering. However, ANY object that has access to the request context object can do this also. This is done by using the `setView()` method in the request context object.


> **Info** Setting a view does not mean that it gets rendered immediately. It means that it is deposited in the request context. The framework will later on in the execution process pick those variables up and do the actual rendering. To do immediate rendering you will use the inline rendering methods describe later on.


```js
component name="general"{

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getModel('MyService').getData();
		// set the view for rendering
		event.setView( view="general/index" );
	
	}

}
```

**View Location (view="general/index"):**

```js
/application
  /views
    /general
      +index.cfm
```

That's it, we use the `setView()` method to set the view `general/index.cfm` to be rendered. Now the cool thing about this, is that we can override the view to be rendered anytime during the flow of the request. So the last event to execute the `setView()` method is the one that counts. Also notice a few things:

* No `.cfm` extension is needed.
* You can traverse directories by using `/` like normal `cfinclude` notation.
* The view can exist in the conventions directory views or in your configured external locations
* You did not specify a layout for the view, so the application's default layout (`main.cfm`) will be used, we will cover this later on.
 
> **Info** Is best practice view locations should simulate handler location + action name. So if the event is general.index, there should be a general folder in the root views folder with a view called index.cfm.

**Let's look at the view code:**

```js
<cfoutput>
<h1>My Cool Data</h1>
#html.table(data=prc.myQuery)#
</cfoutput>
```

I am using our cool HTML Helper (`coldbox.system.core.dynamic.HTMLHelper`) class that is smart enough to render tables, data, HTML 5 elements etc and even bind to ColdFusion ORM entities. All layouts/views have access to the HTML helper.