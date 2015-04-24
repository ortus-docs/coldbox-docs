# How Does It Work

The framework will merge the incoming URL/FORM/REMOTE variables into a single structure called the request collection structure (**RC**) that will live inside the request context object. We also internally create a second collection called the private request collection (**PRC**) that is useful to store data and objects that have no outside effect.

> **Info** As best practice, store data in the private collection and leave the request collection intact with the client's request data.

The request collection lives in the `request` scope and can be accessed from every single part of the framework's life cycle, except the Model layer of course. Therefore, there is one contract and way on how to handle FORM/URL/REMOTE and any other kind of variables. You can consider the request collection to be sort of a super information highway that is unique per request. You will interact with it to get/set values that any part of a request's lifecycle will interact with it. The ColdBox debugger even traces for you how this data structure changes as events are fired during a request.

> **Note** `FORM` variables take precedence 