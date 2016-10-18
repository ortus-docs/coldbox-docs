# How ColdBox Works
ColdBox uses both implicit and explicit invocation methods to execute events and render content back to a user.  You have one single configuration CFC: `config/Coldbox.cfc`, from where you can configure your entire application and a set of folder/file conventions. This configuration file activates certain aspects of your application and configures all the implicit events that mostly reflect the events in the `Application.cfc` that ColdFusion exposes to you.

>Remember that this framework will not solve all your problems. It is a standard, a foundation on which to develop on and thanks to its software programming aspects that it provides.  However, it is up to you to create GOOD code, this is not a magical framework that will make your code better. It will help you, but at the end of the day, it is your responsibility.

## Front Controller
<img src="https://coldbox.ortusbooks.com/content/images/ColdBoxSimpleMVC.png">

ColdBox is loaded by the `Application.cfc` and makes use of the Front Controller design pattern as its means of operation. This means that every request comes in through a single template, usually `index.cfm`. Once a request is received by the framework through this front controller, it will parse the request and re-direct appropriately to the correct event handler controller by looking for an `event` variable in the URL or FORM scopes.

## Request Context
The incoming URL, FORM and REMOTE variables are merged into a single structure that we call the request collection and since we love objects that collection is stored in an object called Request Context. We also create a secondary collection called the private request collection that cannot be affected by the outside world as nothing is merged into it. You can use it for private request variables and the like.

<img src="https://coldbox.ortusbooks.com/content/images/RequestCollectionDataBus.jpg">

The request context object has tons of methods to help you in setting and getting variables from one MVC layer to another, to getting request metadata, rendering RESTful content, setting HTTP headers and more. It is your information super highway for specific requests. Remember that the API Docs are your best friend!

## Request Lifecycle

<img src="https://coldbox.ortusbooks.com/content/images/request-lifecycle.png">

A typical basic request to a ColdBox application looks like this:

* HTTP(S) request is sent from browser to server `http://www.example.com/index.cfm?event=home.about`.
* The request context is created for this request and FORM/URL scopes are populated into the request collection
* The Main ColdBox Event is determined from the `event` variable (`home.about`). (handler is **home** and action is **about**, notice the period separator.
* Event handler controller action is run (`about()` method in `/handlers/home.cfc`)
* The event handler might call a model for business logic
* The view set in the event is rendered (`/views/home/about.cfm`)
* The view’s HTML is wrapped in the rendered layout (`/layouts/main.cfm`)
* Page is returned to the browser

Below you can see the full life-cycle for MVC requests:

![](https://coldbox.ortusbooks.com/content/images/ColdBoxLifecycles.jpg)

### Proxy Lifecycle

ColdBox also has a proxy feature for building SOAP webservices or Flex/Air integration called [ColdBox Proxy](../proxy/index.md).  Below you can see the life-cycle for that process:

![](https://coldbox.ortusbooks.com/content/images/ColdBoxLifecyclesProxy.jpg)
