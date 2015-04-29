# Tips & Tricks

Here are some useful tips for you when doing testing with ColdBox Applications:

* If you are using relative paths, this might be a problem as the running application is different from the test application. Try to always use paths based on the application's AppMapping
* Always use setNextEvent for relocations so they can be mocked
* Use URL variables instead of FORM variables if you will be testing from within Eclipse
* Leverage querySim() for query mocking
* Leverage [MockBox](http://wiki.coldbox.org/wiki/MockBox.cfm) for mocking and stubbing
* If you use [ColdBox Security](http://wiki.coldbox.org/wiki/Security.cfm) or other security mechanisms in your application, you will have to mock a login for integration tests
* Integration tests are NOT the same as handler tests. Handler tests will just test the handler CFC in isolation, so it will be your job to mock everything around it.
* You can use the BaseModelTest to test any domain object
* The ColdBox source testing folder has over 5000 tests, mocking scripts, etc. where you can learn from, so check it out -> https://github.com/ColdBox/coldbox-platform/tree/master/testing

