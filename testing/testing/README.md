# Testing

One of the best things you can do when you develop software applications is TEST! I know nobody likes it, but hey, you need to do it right? With the advocation of frameworks today, you get all these great tools to build your software applications, but how do you test your framework code. ColdBox has revolutionized testing MVC and framework code, since you can unit test your event handlers, interceptors, model objects and even do integration testing and test your entire application with no browser at all.

ColdBox has already all the hooks in place to provide Behavior and Test Driven Development via [TestBox](http://www.ortussolutions.com/products/testbox) and mocking/stubbing capabilities via MockBox.

> TestBox is a testing framework for ColdFusion \(CFML\) that is based on BDD \(Behavior Driven Development\) for providing a clean obvious syntax for writing tests. It also includes MockBox for mocking and stubbing.

## Installing TestBox

So let's get started by opening a CommandBox prompt \(`box`\) in your application root and installing TestBox.

```bash
// stable
install testbox --saveDev

// bleeding edge
install testbox@be --saveDev
```

> **Info** The `--saveDev` flag tells CommandBox to save TestBox locally only for testing purposes as it will not be used to send TestBox for production \([http://commandbox.ortusbooks.com/content/packages/dependencies.html](http://commandbox.ortusbooks.com/content/packages/dependencies.html)\)

Please also note that CommandBox ships with tons of commands for interacting with TestBox and ColdBox testing classes. You can explore them by issuing the following commands or viewing the latest CommandBox API Docs \([http://apidocs.ortussolutions.com/commandbox/current](http://apidocs.ortussolutions.com/commandbox/current)\)

```bash
# testbox namespace help
testbox help

# generations
testbox create help

# run tests
testbox run help

# coldbox creation help
coldbox create help
```

## Benefits

It might be that testing is tedious and takes time to get into the flow of Behavior/Test Driven Development. However, there are incredible benefits to testing:

* Can improve code quality
* Quick error discovery
* Code confidence via immediate verification
* Can expose high coupling
* Encourages refactoring to produce testable code
* Testing is all about behavior and expectations

## Resources

* [Wikipedia](http://en.wikipedia.org/wiki/Unit_test) - [http://en.wikipedia.org/wiki/Unit\_test](http://en.wikipedia.org/wiki/Unit_test)
* [TestBox](http://ortussolutions.com/products/testbox) - [http://ortussolutions.com/products/testbox](http://ortussolutions.com/products/testbox)
* [TestBox BDD Book](http://testbox.ortusbooks.com) - [http://testbox.ortusbooks.com](http://testbox.ortusbooks.com)
* [ColdFusion Builder TestBox Extensions](http://forgebox.io/view/coldbox-platform-utilities) - [http://forgebox.io/view/coldbox-platform-utilities](http://forgebox.io/view/coldbox-platform-utilities)
* [Sublime TestBox Extension](https://github.com/lmajano/cbox-coldbox-sublime) - [https://github.com/lmajano/cbox-coldbox-sublime](https://github.com/lmajano/cbox-coldbox-sublime)
* [VSCode TestBox Extension](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-testbox) - [https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-testbox](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-testbox)

