# RESTFul Data

Out of the box, ColdBox gives you all the RESTFul capabilities you will need to create robust and scalable RESTFul services. Let's add some RESTFul capabilities to our contact listing we created in the previous section.

{% hint style="success" %}
**Tip:** You can find much more information about building ColdBox RESTFul services in our [full docs.](../../digging-deeper/recipes/building-rest-apis.md)
{% endhint %}

## Producing JSON

If you know beforehand what type of format you will be responding with, you can leverage ColdBox 5's auto-marshalling in your handlers. By default, ColdBox detects any return value from handlers and if they are complex it will convert them to JSON automatically for you:

```javascript
any function index( event, rc, prc ){
    return contactService.getAll();    
}
```

That's it! ColdBox detects the array and automatically serializes it to JSON. Easy Peasy!

## renderData\(\)

The request context object has a special function called `renderData()` that can take any type of data and marshall it for you to other formats like `xml, json, wddx, pdf, text, html` or your own type.

{% hint style="success" %}
**Tip**: You can find more information at the API Docs for `renderData()` here [http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html\#renderData\(](http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html#renderData%28)\)
{% endhint %}

So let's open the `handlers/contact.cfc` and add to our current code:

```javascript
any function index( event, rc, prc ){
    prc.aContacts = contactService.getAll();    
    event.renderData( data=prc.aContacts, formats="xml,json,pdf,html" );
}
```

We have added the following line:

```javascript
event.renderData( data=prc.aContacts, formats="xml,json,pdf,html" );
```

This tells ColdBox to render the contacts data in 4 formats: xml, json, pdf and html. WOW! So how would you trigger each format? Via the URL of course.

## Format Detection

ColdBox has the ability to detect formats via URL extensions or an incoming `Accepts` header. If no extension is sent, then ColdBox attempts to determine the format by inspecting the `Accepts` header. If we still can't figure out what format to choose, the default of `html` is selected for you.

```text
# Defaul: The view is presented using no extension or html,cfm
http://localhost:{port}/contacts/index
http://localhost:{port}/contacts/index.html
http://localhost:{port}/contacts/index.cfm

# JSON output
http://localhost:{port}/contacts/index.json
# OR Accepts: application/json

# XML output 
http://localhost:{port}/contacts/index.xml
# OR Accepts: application/xml

# PDF output
http://localhost:{port}/contacts/index.pdf
# OR Accepts: application/pdf
```

{% hint style="success" %}
**Tip:** You can also avoid the extension and pass a URL argument called `format` with the correct format type: `?format=json`.
{% endhint %}

## Routing

Let's add a new route to our system that is more RESTFul than `/contacts/index.json`. You will do so by leveraging the application's router found at `config/Router.cfc`.  Find the `configure()` method and let's add a new route:

```java
route( pattern="/api/contacts", target="contacts.index", name="api.contacts" );
```

The `route()` method allows you to register new URL patterns in your application and immediately route them to a target event.  You can even give it a human readable name that can be later referenced in the `buildLink()` method. 

We have now created a new URL route called `/api/contacts` that if detected will execute the `contacts.index` event. Now reinit the application, why, well we changed the application router and we need the changes to take effect.

{% hint style="success" %}
**Tip: **Every time you add new routes make sure you reinit the application: `http://localhost:{port}/?fwreinit`.
{% endhint %}

You can now visit the new URL pattern and you have successfully built a RESTFul API for your contacts.

```text
http://localhost:{port}/api/contacts.json
```

{% hint style="info" %}
You can find much more about routing in our [full docs](../../the-basics/routing/)
{% endhint %}



