# The Integration Test

Here is the integration test for the `Main` handler.

```text
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

		it( "+doSomething relocates", function(){
			var event = execute( event="main.doSomething" );
			expect(	event.getValue( "setnextevent", "" ) ).toBe( "main.index" );
		});

		it( "+app start fires", function(){
			var event = execute( "main.onAppInit" );
		});

		it( "+can handle exceptions", function(){
			//You need to create an exception bean first and place it on the request context FIRST as a setup.
			var exceptionBean = createMock( "coldbox.system.web.context.ExceptionBean" )
				.init( erroStruct=structnew(), extramessage="My unit test exception", extraInfo="Any extra info, simple or complex" );

			// Attach to request
			getRequestContext().setValue( name="exception", value=exceptionBean, private=true );

			//TEST EVENT EXECUTION
			var event = execute( "main.onException" );
		});

		describe( "Request Events", function(){

			it( "+fires on start", function(){
				var event = execute( "main.onRequestStart" );
			});

			it( "+fires on end", function(){
				var event = execute( "main.onRequestEnd" );
			});

		});

		describe( "Session Events", function(){

			it( "+fires on start", function(){
				var event = execute( "main.onSessionStart" );
			});

			it( "+fires on end", function(){
				//Place a fake session structure here, it mimics what the handler receives
				URL.sessionReference = structnew();
				URL.applicationReference = structnew();
				var event = execute( "main.onSessionEnd" );
			});

		});


	});

}
```

