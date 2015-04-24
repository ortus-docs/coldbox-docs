# How Does It Work

The framework will merge the incoming URL/FORM/REMOTE variables into a single structure called the request collection structure (**RC**) that will live inside the request context object. We also internally create a second collection called the private request collection (**PRC**) that is useful to store data and objects that have no outside effect.

> **Info** As best practice, store data in the private collection and leave the request collection intact with the client's request data.

The request collection lives in the request scope and can be accessed from every single part of the framework's life cycle, except the Model layer of course. Therefore, there is one contract and way on how to handle FORM/URL/REMOTE and any other kind of variables. You can consider the request collection to be sort of a super information highway that is unique per request. You will interact with it to get/set values that any part of a request's lifecycle will interact with it. The ColdBox debugger even traces for you how this data structure changes as events are fired during a request.

![](RequestCollectionDataBus.jpg)

The request context objects makes it much easier for you to have access to variables, since you will no longer be scoping them. It also encapsulates any other scopes you would like to merge into the collection. Another important aspect of the request context is that it also contains several metadata and utility methods that you can use to build your applications. Some of these methods are covered below, but the best place to learn about all of its methods is to see the [API Docs](http://apidocs.coldbox.org/).

Note: FORM variables take precedence 