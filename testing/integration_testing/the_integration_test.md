# The Integration Test

Now that we have seen the handler code, let's see the testing code as well. One important thing to note is that I use URL variables in my tests instead of FORM variables. I use URL because if the tests are executed via the MXUnit Eclipse Plugin they execute via SOAP and ColdFusion does not translate the FORM structure in SOAP web services. If you only test via the browser then you are ok, but if you use the Eclipse Plugin you must use URL scope instead in your tests. The great thing about ColdBox is that it abstracts FORM and URL scope into the request collections!

```js
component extends="coldbox.system.testing.BaseTestCase" appmapping="/apps/MyApp"{

	function setup(){
		super.setup();
	}

	function testindex(){
		var event = execute("general.index");
		assertEquals("Welcome to ColdBox!", event.getValue("welcomeMessage"));
    }
	
    function testdoSomething(){
    	var event = execute("general.doSomething");
		assertEquals("general.index", event.getValue("setnextevent") );
	}

	function testdspLogin(){
		var event = execute("general.dspLogin");
		var prc = event.getCollection(private=true);
		assertEquals("general.dspLogin", prc.currentView );
	}

	function testdoLoginWithNoData(){
		var event = execute("general.doLogin");
		assertTrue( getFlashScope().exists("notice") );
		assertEquals("general.dspLogin", event.getValue("setnextevent") );
	}

	function testDoLoginWithInvalidData(){
		URL.username = "joe";
		URL.password = "hacker";
		var event = execute("general.doLogin");
		assertTrue( getFlashScope().exists("notice") );
		assertEquals("general.dspLogin", event.getValue("setnextevent") );
	}

	function testdoLoginWithGoodData(){
		URL.username = "luis";
		URL.password = "luis";
		var event = execute("general.doLogin");
		assertFalse( getFlashScope().exists("notice") );
		assertEquals("general.hello", event.getValue("setnextevent") );
	}

	function testhello(){
		var event = execute("general.hello");
		assertEquals("Howdy user!", event.getValue("cbox_handler_results") );
	}

}
```
