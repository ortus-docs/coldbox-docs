# Service Layer Is The Model

I want to apply best practices and use a service layer approach for my application and model design. I will then use these service objects in my handlers in order to do the business logic for me. Repeat after me: 

> I WILL NOT PUT BUSINESS LOGIC IN EVENT HANDLERS!

The whole point of the model layer is that it is separate from the other 2 layers (controller and views). Remember, the model is supposed to live on its own and not be dependent on external layers (Decoupled). From these simple requirements I will create the following classes:

* `BookService.cfc` - A service layer for book operations
* `Book.cfc` - Represents a book in my system

![](../../images/ServiceLayers.jpg)

## What is a service layer?

> A Service Layer defines an application's boundary [Cockburn PloP] and its set of available operations from the perspective of interfacing client layers. It encapsulates the application's business logic, controlling transactions and coor-dinating responses in the implementation of its operations. 
<small>[Martin Fowler](http://martinfowler.com/eaaCatalog/serviceLayer.html)</small>

A service layer approach is a way to architect enterprise applications in which there is a layer that acts as a service or mediator to your domain models, data layers and so forth. This layer is the one that event handlers or remote ColdBox proxies can talk to in order to interact with the domain model. There are basically two approaches when building a service layer:

1. Designing the services around functionality that might encompass several domain model objects. This is our preferred approach as it creates a more rich service layer and you will not have class explosion.
2. Designing a service for each business object, which in turn matches a table or set of tables in the permanent storage. This approach is easy to follow, but the consequences are the responsibilities that could be grouped are not and you will end up with class explosion.

The best way to determine what you prefer or need is to actually try both approaches. Once you design them and put them in practice, you will find your preference. I want to concentrate and challenge you to try these approaches out and learn from your experiences. I believe there is NO SILVER BULLET on OO design, just stick to best practices and practice code smell, the art of knowing when your code is not right and therefore smells nasty!

## The Book Services

![](../../images/MVC+ORM.png)

The `BookService` object will be my API to do operations as mentioned in my requirements and this is the object that will be used by my handlers. My `Book` object will model a Book's data and behavior. It will be produced, saved and updated by the `BookService` object and will be used by event handlers in order to populate and validate them with data from the user. 

The view layer will also use the `Book` object in order to present the data. As you can see, the event handlers are in charge of talking to the Domain Model for operations/business logic, controlling the user's input requests, populating the correct data into the `Book` model object and making sure that it is sent to the book service for persistence.

