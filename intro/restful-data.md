# RESTFul Data

Out of the box, ColdBox gives you all the RESTFul capabilities you will need to create robust and scalable RESTFul services.  Let's add some RESTFul capabilities to our contact listing we created in the previous section.

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

ColdBox has the ability to detect formats via URL extensions.  Meaning you can hit the event but adding and extension to the URL.  If no extension is sent then the default of `html` content is selected for you.

```
# Default HTML output: The view is presented
http://localhost:{port}/contacts/index

# JSON output
http://localhost:{port}/contacts/index.json

# XML output 
http://localhost:{port}/contacts/index.json


