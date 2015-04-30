# The Handler To Test

Let's use a sample event handler so we can test it:

```js
component{

	function index(event,rc,prc){
		rc.welcomeMessage = "Welcome to ColdBox!";
        event.setView("general/index");
    }
	
    function doSomething(event,rc,prc){
		setNextEvent("general.index");
	}

	function doLogin(event,rc,prc){
		event.paramValue("username","");
		event.paramValue("password","");
		
		if( !len(rc.username) or !len(rc.password) ){
			flash.put("notice","Username and password must be filled out!");
			setNextEvent("general.dspLogin");
		}

		if( rc.username == "luis" and rc.password = "luis" ){
			setNextEvent("general.hello")
		}
		else{
			flash.put("notice","Invalid Credentials! Try Again!");
			setNextEvent("general.dspLogin");
		}	
	}

	function hello(event,rc,prc){
		return "Howdy user!";
	}

}
```

## Mocking Relocations

I can test this entire handler without me building any views yet. I can even test the relocations that happen via `setNextEvent()`. ColdBox will wire itself up with some mocking classes to intercept those relocations for you and place those values in the request collection for you so you can assert them. It creates a key called setnextevent in the request collection and any arguments passed to the method are also saved as keys with the following pattern:

```js
setnextevent_{argumentName}
```

> **Caution** Any relocation produced by the framework via the setNextEvent method will produce some variables in the request collection for you to verify relocations. 

