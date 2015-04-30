# Developer Focus


This section is writtent for developers, so what is our focus? Our focus is to get awareness about the different aspects of testing and what are some of the testing focuses for the development phase:

* Unit Testing - We should be testing all the CFCs we create in our applications. Especially when dealing with Object Oriented systems, we need to have mocking and stubbing capabilities (See MockBox).
* Integration Testing - Unit tests can only expose issues at the CFC level, but what happens when we put everything together? Integration testing in ColdBox allows you to test requests just like if they where executed from the browser. So all your application loads, interceptions, caching, dependency injection, ORM, database, etc.
* UI Integration Testing - Testing verification via the UI using tools like Selenium is another great idea!
* Load Testing - There are tons of free load testing tools and they are easy to test how our code behaves under load. Don't open a browser with 5 tabs on it and start hitting enter! We have been there and it don't work that great :)


