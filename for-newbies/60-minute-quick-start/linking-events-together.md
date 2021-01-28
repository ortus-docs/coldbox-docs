# Linking Events Together

ColdBox provides you with a nice method for generating links between events by leveraging an object called `event` that is accessible in all of your layouts/views and event handlers. This `event` object is called behind the scenes the **request context object,** which models the incoming request and even contains all of your incoming `FORM` and `URL` variables in a structure called `rc`.

{% hint style="success" %}
**Tip**: You will use the event object to set views, set layouts, set HTTP headers, read HTTP headers, convert data to other types \(json,xml,pdf\), and much more.
{% endhint %}

## Building Links

The method in the request context that builds links is called: `buildLink()`. Here are some of the arguments you can use:

```java
/**
 * Builds links to events or URL Routes
 *
 * @to The event or route path you want to create the link to
 * @queryString The query string to append which can be a regular 
               query string string, or a struct of name-value pairs
 * @translate Translate between . to / depending on the SES mode on to and queryString arguments. Defaults to true.
 * @ssl Turn SSl on/off on URL creation, by default is SSL is enabled, we will use it.
 * @baseURL If not using SES, you can use this argument to create your own base url apart from the default of index.cfm. Example: https://mysample.com/index.cfm
 */
string function buildLink(
	to,
	queryString       = "",
	boolean translate = true,
	boolean ssl,
	baseURL = ""
)
```

## Edit Your View

Edit the `views/virtual/hello.cfm` page and wrap the content in a `cfoutput` and create a link to the main ColdBox event, which by convention is `main.index`.

```markup
<cfoutput>
    <h1>Hello from ColdBox Land!</h1>
    <p><a href="#event.buildLink( "main" )#">Go home</a></p>
</cfoutput>
```

This code will generate a link to the `main.index` event in a search engine safe manner and in SSL detection mode. Go execute the event: `http://localhost:{port}/virtual/hello` and click on the generated URL, you will now be navigating to the default event `/main/index`. This technique will also apply to FORM submissions:

```markup
<form action="#event.buildLink( 'user.save' )#" method="post">
...
</form>
```

{% hint style="success" %}
**Tip** You can visit our API Docs for further information about the `event` object and the `buildLink` method: [http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html](http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html).

For extra credit try to use more of the `buildLink` arguments.
{% endhint %}

## URL Structure & Mappings

ColdBox allows you to manipulate the incoming URL so you can create robust URL strategies especially for RESTFul services. This is all done by convention and you can configure it via the application router: `config/Router.cfc` for more granular control.

{% hint style="info" %}
Out of the box we provide you with convention based routing that maps the URL to modules/folders/handlers and actions.

`route( "/:handler/:action" ).end()`
{% endhint %}

We have now seen how to execute events via nice URLs. Behind the scenes, ColdBox translates the URL into an executable event string just like if you were using a normal URL string:

* `/main/index` -&gt; `?event=main.index`
* `/virtual/hello` -&gt; `?event=virtual.hello`
* `/admin/users/list` -&gt; `?event=admin.users.list`
* `/handler/action/name/value` -&gt; `?event=handler.action&name=value`
* `/handler/action/name` -&gt; `?event=handler.action&name=`

By convention, any name-value pairs detected after an event variable will be treated as an incoming `URL` variables. If there is no pair, then the value will be an empty string.

{% hint style="success" %}
**Tip:** By default the ColdBox application templates are using full URL rewrites. If your web server does not support them, then open the `config/Router.cfc` and change the full rewrites method to **false**: `setFullRewrites( false ).`
{% endhint %}

