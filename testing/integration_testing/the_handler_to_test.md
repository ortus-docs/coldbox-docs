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

	function dspLogin(event,rc,prc){
		event.setView("general/dspLogin");
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


