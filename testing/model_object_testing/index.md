# Model Object Testing

You can now test model objects directly with no need of doing integration testing. This way you can unit test model objects very very easily using great mocking capabilities. All you need to do is the following:

1. Create a test class that inherits from coldbox.system.testing.BaseModelTest
2. Create a component annotation called model that equals the full path of the model object CFC to target for unit testing

This testing support class will create your model object, and decorate with mocking capabilities via MockBox and create some mocking classes you might find useful in your model object unit testing. The following are the objects that are placed in the variables scope for you to use:

* model : The target model object to test
* mockLogger : A mock logger class
* mockLogBox : A mock LogBox class
* mockCacheBox : A mock Cache Factory class
* mockWireBox : A mock WireBox Injector class

> **Important** We do not initialize your model objects for you. This is your job as you might need some mocking first. 

Basic Setup

```js
component extends="coldbox.system.testing.BaseModelTest" model="myApp.model.User"{

  function setup(){
     super.setup();
     user = model;
  }

  function testisActive(){
    assertEquals( false, user.isActive() );
  }

}
```

