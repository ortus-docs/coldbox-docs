# Mocking HTTP Methods

There might be a case when you are building RESTful applications that you need to test how your handlers respond via multiple HTTP verbs. If you use the MXUnit Eclipse Plugin, this uses only a POST approach, so even your simple GET operations could fail. So here is a little snippet of how to mock the HTTP verb before an execution in a test case:

```js
function testList(){
	// mock HTTP verb to GET
	getMockBox().prepareMock( getRequestContext() ).$("getHTTPMethod","GET");
	// execute now as a GET
	var event = execute("users.list");
}
```
