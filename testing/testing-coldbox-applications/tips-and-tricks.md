# Tips & Tricks

Here are some useful tips for you when doing testing with ColdBox Applications:

* If you are using relative paths in your application, you might encounter problems since the running application is different from the test application. Try to always use paths based on the application's `AppMapping`
* Always use `setNextEvent()` for relocations so they can be mocked
* Leverage `querySim()` for query mocking
* Leverage [MockBox](https://testbox.ortusbooks.com/mocking/mockbox) for mocking and stubbing
* Integration tests are NOT the same as handler tests. Handler tests will just test the handler CFC in isolation, so it will be your job to mock everything around it.
* You can extend the `coldbox.system.testing.BaseModelTest` to test any domain object
* The [ColdBox source code testing folder](https://github.com/ColdBox/coldbox-platform/tree/master/tests) has over 5,000 tests, mocking scripts and more for you to learn from

