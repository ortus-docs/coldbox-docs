# Renderdata With Formats

This method has two powerful arguments: formats & formatsView. If you currently have code like this:

```js
event.paramValue("format", "html");

switch( rc.format ){
	case "json" : case "jsonp" : case "xml" : {
		event.renderData(data=mydata, type=rc.format);
		break;
	} 
	case "pdf" : {
		event.renderData(data=renderView("even/action"), type="pdf");
		break;
	}
	case "html" : {
		event.setView( "event/action" );
		break;
  	}
};
```

Where you need to param the incoming format extension, then do a switch and do some code for marshalling data into several formats. Well, no more, you can use our formats argument and ColdBox will marshall and code all that nasty stuff for you:

```js
event.renderData(data=MyData, formats="xml,json,html,pdf");
```

That's it! ColdBox will figure out how to deal with all the passed in formats for you that *renderdata* can use. By convention it will use the name of the incoming event as the view that will be rendered for HTML and PDF; implicit views. So if the event was users.list then the view would be views/users/list.cfm. However, you can tell us which view you like if it is named different:

```js
event.renderData(data=MyData, formats="xml,json,html,pdf", formatsView="data/MyView");
```
