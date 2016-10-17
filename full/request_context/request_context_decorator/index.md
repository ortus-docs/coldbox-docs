# Request Context Decorator


## Introduction

The request context object is bound to the framework release and as we all know, each application is different in requirements and architecture. Thus, we have the application of the Decorator Pattern to our request context object in order to help developers program to their needs. So what does this mean? Plain and simply, you can decorate the ColdBox request context object with one of your own. You can extend the functionality to the specifics of the software you are building. You are not modifying the framework code base, but extending it and building on top of what ColdBox offers you.

> "A decorator pattern allows new/additional behavior to be added to an existing method of an object dynamically." <small>Wikipedia </small>

![](.././images/DecoratorPattern.png)