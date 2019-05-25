# Testing ColdBox Applications

One of the best things you can do when you develop software applications is TEST! I know nobody likes it, but hey, you need to do it right? With the advocation of frameworks today, you get all these great tools to build your software applications, but how do you test your framework code. ColdBox has revolutionized testing HMVC and framework code, since you can unit test your event handlers, interceptors, model objects and even do **integration testing and true BDD \(Behavior Driven Development\)** and test your entire application with no browser at all.

ColdBox has already all the hooks in place to provide Behavior and Test Driven Development via [TestBox](http://www.ortussolutions.com/products/testbox) and mocking/stubbing capabilities via MockBox.

{% hint style="info" %}
**TestBox** is a testing framework for ColdFusion \(CFML\) that is based on BDD \(Behavior Driven Development\) for providing a clean obvious syntax for writing tests. It also includes MockBox for mocking and stubbing.

**TestBox is included with all ColdBox templates.**
{% endhint %}

## Installing TestBox

TestBox already comes defined in the `box.json` of all ColdBox application templates.  However, if you have a custom ColdBox app or a custom template, then you can easily install it via CommandBox:

```bash
// stable
install testbox --saveDev

// bleeding edge
install testbox@be --saveDev
```

{% hint style="info" %}
The --saveDev flag tells CommandBox to save TestBox locally only for testing purposes as it will not be used to send TestBox for production \([http://commandbox.ortusbooks.com/content/packages/dependencies.html](http://commandbox.ortusbooks.com/content/packages/dependencies.html)\)
{% endhint %}

Please also note that CommandBox ships with tons of commands for interacting with TestBox and ColdBox testing classes. You can explore them by issuing the following commands or viewing the latest CommandBox API Docs \([http://apidocs.ortussolutions.com/commandbox/current](http://apidocs.ortussolutions.com/commandbox/current)\)

```bash
# testbox namespace help
testbox help

# generations
testbox create help

# run tests
testbox run help

# Watch your files and execute the tests if they change
testbox watch

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

**Our recommendation is to do BDD and integration testing first.  Unit testing is important but it is more important to verify that your requirements are met.**

## Resources

* [Wikipedia](http://en.wikipedia.org/wiki/Unit_test) - [http://en.wikipedia.org/wiki/Unit\_test](http://en.wikipedia.org/wiki/Unit_test)
* [TestBox](http://ortussolutions.com/products/testbox) - [http://ortussolutions.com/products/testbox](http://ortussolutions.com/products/testbox)
* [ColdFusion Builder TestBox Extensions](http://forgebox.io/view/coldbox-platform-utilities) - [http://forgebox.io/view/coldbox-platform-utilities](http://forgebox.io/view/coldbox-platform-utilities)
* [Sublime TestBox Extension](https://github.com/lmajano/cbox-coldbox-sublime) - [https://github.com/lmajano/cbox-coldbox-sublime](https://github.com/lmajano/cbox-coldbox-sublime)
* [VSCode TestBox Extension](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-testbox) - [https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-testbox](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-testbox)

