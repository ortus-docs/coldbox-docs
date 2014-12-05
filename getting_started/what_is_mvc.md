# What is MVC

![](../images/mvc-overview.png)

<br>

>"A developer often wishes to separate data (model) and user interface (view) concerns, so that changes to the user interface will not affect data handling, and that the data can be reorganized without changing the user interface. The model-view-controller solves this problem by decoupling data access and business logic from data presentation and user interaction, by introducing an intermediate component: the controller." [Wikipedia](http://en.wikipedia.org/wiki/Model-view-controller)

MVC is a popular design pattern called [Model View Controller](http://en.wikipedia.org/wiki/Model–view–controller) which seeks to promote good maintainable software design by separating your code into 3 main tiers.

## Model
The Model is the heart of your application.  Your business logic should mostly live here in the form of services, beans, and DAOs. 

## Controllers
Controllers are the traffic cops of your application. They direct flow control, and interface directly without incoming parameters from form and URL scopes. It is the controller’s job to communicate with the appropriate models for processing, and set up either a view to display results or return marshalled data like JSON, XML, PDF, etc. 

## Views
The Views are what the users see and interact with. They are the templates used to render your application out for the web browser. Typically this means HTML. 

## ColdBox MVC
ColdBox embraces this standard and provides customizable conventions for how your app will be organized in a way that makes sense to programmers who have to maintain your code in the future.
