# Testing Handlers

ColdBox offers two approaches to testing your event handlers:

* **Integration Testing** : Tests everything top-down in your application
* **Handler Testing** : Like unit testing for handlers

The main difference is that integration testing will virtually create your application and execute the event you want. Thus, loading everything in a typical request and simulate it. This is a fantastic approach as it lets you test like you would from a browser. The handler testing just tests the event handler in isolation much like unit testing does. To get started with testing, please visit our [Testing](../../testing/testing/) section.

