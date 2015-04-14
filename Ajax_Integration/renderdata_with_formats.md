# RenderData With Formats

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

