# Testing

One of the best things you can do when you develop software applications is TEST! I know nobody likes it, but hey, you need to do it right? With the advocation of frameworks today, you get all these great tools to build your software applications, but how do you test your framework code. ColdBox has revolutionized testing MVC and framework code, since you can unit test your event handlers, interceptors, model objects and even do integration testing and test your entire application with no browser at all. 

ColdBox has already all the hooks in place to provide Behavior and Test Driven Development via [TestBox](http://www.ortussolutions.com/products/testbox) and mocking/stubbing capabilities via MockBox. So let's get started by opening a CommandBox prompt in your application and installing TestBox.

```bash
// stable
install testbox --saveDev

// bleeding edge
install testbox-be --saveDev
```

> **Info** The `--saveDev` flag tells CommandBox to save TestBox locally only for testing purposes as it will not be used to send TestBox for production (http://commandbox.ortusbooks.com/content/packages/dependencies.html)


## Benefits
It might be that testing is tedious and takes time to get into the flow of Behavior/Test Driven Development. However, there are incredible benefits to testing:

* Can improve code quality
* Quick error discovery
* Code confidence via immediate verification
* Can expose high coupling
* Encourages refactoring to produce testable code
* Testing is all about behavior and expectations

### Resources

* [Wikipedia](http://en.wikipedia.org/wiki/Unit_test)
* [MXUnit](http://mxunit.org/)
* [ColdBox CheatShee](http://www.coldbox.org/downloads/ColdboxCheatSheet.pdf)t

