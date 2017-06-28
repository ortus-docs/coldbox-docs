# Rendering Results

The `execute()` method has an argument called `renderResults` which defaults to **false**. If you pass in **true** then ColdBox will go through the normal rendering procedures and save the results in a request collection variable called: `cbox_rendered_content` and expose to you a method in the request context called `getRenderedContent()`. It will even work with `renderData()` or if you are returning RESTful information. Then you can easily assert what the content would have been for an event.

```js
it( "can render users", function(){
   var event = execute( event="rest.api.users", renderResults=true );
   expect( event.getValue( "cbox_rendered_content" ) ).toBeJSON();
} );
```

## `getRenderedContent()`

In testing mode, the ColdBox request context `event` object has a convenience method called `getRenderedContent()` that will give you the rendered content instead of you going directly to the request context and finding the `cbox_rendered_content` variable.

```js
it( "can render users", function(){
   var event = execute( event="rest.api.users", renderResults=true );
   expect( event.getRenderedContent() ).toBeJSON();
} );
```
