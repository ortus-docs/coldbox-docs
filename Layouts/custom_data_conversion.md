# Custom Data Conversion

You can do custom data conversion by convention when marshalling CFCs. If you pass in a CFC as the data argument and that CFC has a method called $renderdata(), then the marshalling utility will call that function for you instead of using the internal marshalling utilities. You can pass in the custom content type for encoding as well:

The call to *renderdata* from your handlers: 

```js
// get an instance of your custom converter
myConverter = getModel("MyConverter")
// put some data in it
myConverter.setData( data );
// marshall it out according to your conversions and the content type it supports
event.renderData(data= myConverter, contentType=myConverter.getContentType());
```

The CFC converter: 

```js
component accessors="true"{

	property name="data" type="mytype";
	property name="contentType";
	
	function init(){ 
		setContentType("text");
		return this; 
	}
	
	// The magical rendering
	function $renderdata(){
		var d = {
			n = data.getName(),
			a = data.getAge(),
			c = data.getCoo(),
			today = now()
		};
		
		return d.toString();
	}

}
```

In this approach your `$renderdata()` function can be much more customizable than our internal serializers. Just remember to use the right contentType argument so the browser knows what to do with it. 