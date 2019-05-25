# Handler Returning Results



## Handler Returning Results

If you have written event handlers that actually return data, then you will have to get the values from the request collection. The following are the keys created for you in the request collection.

| Key | Description |
| :--- | :--- |
| `cbox_handler_results` | The value returned from the event handler method. This key will NOT be created if the handler does not return any data. |

**The handler** \(`main.cfc`\)

```text
function list(event,rc,prc){
    return "Hola Luis";
}
```

**The spec**

```text
it( "+homepage renders", function(){
	var event = execute( event="main.list", renderResults=true );
	expect(	event.getValue( name="cbox_handler_results" ) ).toBe( "Hola Luis" );
});
```

