# HTTP Method Mocking

There are times when building RESTFul web services that you need to mock the incoming HTTP verb.  This is done via MockBox:

```js
function run(){

    describe( "Main Handler", function(){
    
      beforeEach(function( currentSpec ){
        setup();
      });
    
      it( "+homepage renders", function(){
        prepareMock( getRequestContext() )
    	    .$(“getHTTPMethod”, “DELETE”);
        FORM.userID = 123;
        var event = execute( event=“user.delete“ );
      });	
    });
}
```