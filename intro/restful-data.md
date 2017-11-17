# RESTFul Data

Out of the box, ColdBox gives you all the RESTFul capabilities you will need to create robust and scalable RESTFul services.  Let's add some RESTFul capabilities to our contact listing we created in the previous section.

> **Tip** You can find much more information about building ColdBox RESTFul services in our [full docs.](../full/recipes/building_rest_apis.md)

## renderData()

The request context object has a special function called `renderData()` that can take any type of data and marshall it for you to other formats like xml, json, wddx, pdf, text, html or your own type.

> **Tip** You can find more information at the API Docs for `renderData()` here http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html#renderData()

So let's open the `handlers/contact.cfc` and add to our current code:

```js
any function index( event, rc, prc ){
    prc.aContacts = contactService.getAll();    
    event.renderData( data=prc.aContacts, formats="xml,json,pdf,html" );
}
```

We have added the following line:

```js
event.renderData( data=prc.aContacts, formats="xml,json,pdf,html" );
```

Instead of the `event.setView()` method call we had before.  This tells ColdBox to render the contacts data in 3 formats: xml, json, and html.  So how would you trigger each format? Via the URL of course.

## Format Detection

ColdBox has the ability to detect formats via URL extensions.  Meaning you can hit the event but adding an extension to the URL.  If no extension is sent, then ColdBox attempts to determine the format by inspecting the `Accepts` header.  If we still can't figure out what format to choose, the default of `html` is selected for you.

```
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

> **Tip** You can also avoid the extension and pass a URL argument called `format` with the correct format type: `?format=json`.

You have now created a RESTFul service for your contacts listing, enjoy it.

## Routing

Let's add a new route to our system that is more RESTFul than `/contacts/index.json`.  You will do so by leveraging the application's routing file found at `config/routes.cfm`.  Open it and add the following new route pattern before the ColdBox default route:

```
addRoute( 
    pattern = "/api/contacts",
    handler = "contacts",
    action = "index"
);
```

The `addRoute()` method allows you to register new URL patterns in your application.  We have now created a new URL route called `/api/contacts` that if detected will execute the `contacts.index` event.  Now reinit the application.

> **Tip** Every time you add new routes make sure you reinit the application: `http://localhost:{port}/?fwreinit`.

You can now visit the new URL pattern and you have successfully built a RESTFul API for your contacts.

```
http://localhost:{port}/api/contacts.json
```

> **Note** You can find much more about routing in our [full docs](../full/routing/index.md)
