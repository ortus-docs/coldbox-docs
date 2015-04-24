# Best Practices

##Organization

Always try to have some kind of mapping between the different logical sections or modules of your application and their event handlers. For example, if the application has a section for user management, with master and detail views, create a single event handler CFC to hold all the methods related to that module. Now, large sections of your application that are more complex and have lots of actions and views, may require you to split the event handlers even more (like packages/directories), or [ColdBox Modules](../modules/index.md).

The handler CFCs are also objects and therefore they should have their specific identity and function. So you need to map them in the best logical way according to their functions. So think of them as entities and how they would do tasks for you via the events. Once you do this, you can come up with a very good cohesive event model for your applications. 

In conclusion, organize your handlers as you would a domain model, put in practice your [Ontology](http://en.wikipedia.org/wiki/Ontology) skills and define what these handlers will do for your, what is their identity.

#Executing other events (Event Chaining)

The best practice on event execution would be via the runEvent() method. However, please note that running events via this method does a complete life cycle execution. So do not abuse it. If you find that you need to chain events or are getting more complex, we suggest upgrading your code to use [ColdBox Interceptors](http://wiki.coldbox.org/wiki/EventHandlers.cfm#Event_Caching). This is a much more flexible and decoupled way of providing executing code chains.

#Naming Conventions

Try always to add meaningful names to methods so other developers and users can understand what you are doing.

