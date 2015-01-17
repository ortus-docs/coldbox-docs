# What is MVC

![](../images/mvc-overview.png)

<br>

>"A developer often wishes to separate data (model) and user interface (view) concerns, so that changes to the user interface will not affect data handling, and that the data can be reorganized without changing the user interface. The model-view-controller solves this problem by decoupling data access and business logic from data presentation and user interaction, by introducing an intermediate component: the controller." [Wikipedia](http://en.wikipedia.org/wiki/Model-view-controller)

<br>
MVC is a popular design pattern called [Model View Controller](http://en.wikipedia.org/wiki/Model–view–controller) which seeks to promote good maintainable software design by separating your code into 3 main tiers.

## Model
The Model is the heart of your application.  Your business logic should mostly live here in the form of services, beans, and DAOs.  In ColdBox, all your model objets are managed by WireBox (Dependency Injection Framework).

## Controllers
Controllers are the traffic cops of your application. They direct flow control, and interface directly without incoming parameters from form and URL scopes. It is the controller’s job to communicate with the appropriate models for processing, and set up either a view to display results or return marshalled data like JSON, XML, PDF, etc. In ColdBox, controllers are refered to as **event handlers**.

## Views
The Views are what the users see and interact with. They are the templates used to render your application out for the web browser. Typically this means HTML.

## ColdBox MVC
ColdBox embraces this standard and provides customizable conventions for how your app will be organized in a way that makes sense to programmers who have to maintain your code in the future.

An important but unknown fact about these definitions, is the fact that data gets passed around from each of the three layers. How?

Frameworks or patters never explain this, it is just assumed. Well, this data object in ColdBox terms is called the **Request Collection**, which we will cover later on. It is a data structure that lives in the `request` scope, so it is unique per request, and it models a user's request. It is available to all parts of the running framework application and it is contained inside of an object called the *request context*.

If you don't need or understand the added separation of business logic into a model layer or find that maintaining models requires more complexity than you want or have experienced before, you can ignore them and build your application minimally using Event Handlers and Views. However, you should know that this is **highly discouraged** and if you will be mastering object oriented web applications, you should develop and separate logic into a domain model. The choice is yours.

## Resources

* http://en.wikipedia.org/wiki/Domain_model
* http://domaindrivendesign.org/
* http://martinfowler.com/eaaCatalog/domainModel.html






