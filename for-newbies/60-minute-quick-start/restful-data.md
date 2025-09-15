# RESTFul Data

Out of the box, ColdBox gives you all the RESTFul capabilities you will need to create robust and scalable RESTFul services. Let's add some RESTFul capabilities to our contact listing we created in the previous section.

{% hint style="success" %}
**Tip:** You can find much more information about building ColdBox RESTFul services in our [full docs.](../../digging-deeper/recipes/building-rest-apis.md)
{% endhint %}

## Producing JSON

If you know beforehand what type of format you will be responding with, you can leverage ColdBox auto-marshaling in your handlers. By default, ColdBox detects **any** return value from handlers, and if they are complex, it will convert them to JSON automatically for you:

{% code title="contacts.cfc" %}
```javascript
any function data( event, rc, prc ){
    return contactService.getAll();    
}
```
{% endcode %}

ColdBox detects the array and automatically serializes it to JSON. Easy Peasy!

## renderData()

The request context object has a special function called `renderData()` that can take any type of data and marshall (convert) it for you to other formats like `xml, json, wddx, pdf, text, html` or your own type.

{% hint style="success" %}
**Tip**: You can find more information at the API Docs for `renderData()` here [http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html#renderData(](http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html#renderData\())
{% endhint %}

So let's open the `handlers/contacts.cfc` and lets create a new action called `data`

{% code title="contacts.cfc" %}
```javascript
any function data( event, rc, prc ){
    prc.aContacts = contactService.getAll();    
    event.renderData( data=prc.aContacts, formats="xml,json,pdf" );
}
```
{% endcode %}

This tells ColdBox to render the contacts data in 4 formats: XML, JSON, pdf, and HTML. WOW! So how would you trigger each format? Via the URL, of course.

## Format Detection

ColdBox has the ability to detect formats via URL extensions or an incoming `Accepts` header. If no extension is sent, then ColdBox attempts to determine the format by inspecting the `Accepts` header. If we still can't figure out what format to choose, the default of `html` is selected for you.

```
# Default: The view is presented using no extension or html,cfm
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

Let's add a new route to our system that is more RESTFul than `/contacts/index.json`. You will do so by leveraging the application's router found at `config/Router.cfc`. Find the `configure()` method, and let's add a new route:

```cfscript

// Restuful Route
route( "/api/contacts" )
    .as( "api.contacts" )
    .rc( "format", "json" )
    .to( "contacts.data" );

// Default Route
route( ":handler/:action?" ).end();
```

We have registered the API route and also defaulted the format to `JSON`. Try it out.

{% hint style="danger" %}
Make sure you add routes above the default ColdBox route. If not, your route will never fire.
{% endhint %}

{% hint style="success" %}
**Tip:** Every time you add new routes, make sure you reinit the application: `http://localhost:{port}/?fwreinit`.
{% endhint %}

You can now visit the new URL pattern, and you have successfully built a RESTFul API for your contacts.

```
http://localhost:{port}/api/contacts.json
```

{% hint style="info" %}
You can find much more about routing in our [full docs](../../the-basics/routing/)
{% endhint %}
