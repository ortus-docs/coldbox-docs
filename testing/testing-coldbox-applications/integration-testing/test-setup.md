# Test Setup

Here is a spec written for you. Please note that in the `beforeEach()` life-cycle method you need to execute the `setup()` method will will setup a new ColdBox request for each spec you run.

```text
component extends="coldbox.system.testing.BaseTestCase" appMapping="/apps/MyApp"{
	
	function run(){

		describe( "Main Handler", function(){

			beforeEach(function( currentSpec ){
				// Setup as a new ColdBox request, VERY IMPORTANT. ELSE EVERYTHING LOOKS LIKE THE SAME REQUEST.
				setup();
			});

			it( "+homepage renders", function(){
				var event = execute( event="main.index", renderResults=true );
				expect(	event.getValue( name="welcomemessage", private=true ) ).toBe( "Welcome to ColdBox!" );
			});
		
		});
	}

}
```

