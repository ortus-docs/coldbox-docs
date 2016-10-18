# Adding Event Handlers

We have now seen how to add handlers via CommandBox using the `coldbox create handler` command and also execute them by convention by leveraging the following URL pattern:

```
http://localhost:{port}/folder/handler/action
http://localhost:{port}/handler/action
http://localhost:{port}/handler
```

Also remember, that if no `action` is defined in the incoming URL then the default action of `index` will be used.

## Working With Incoming Data

Now, let's open the handler we created before called `handlers/hello.cfc` and add some public and private variables to it so our views can render the variables.

```js

function index( event, rc, prc ){
 // param an incoming variable.
 event.paramValue( "name", "nobody" );
 // set a private variable
 prc.when = dateFormat( now(), "full" );
 // set the view to render
 event.setView( "hello/index" );
}

```

Let's open the view now: `views/hello/index.cfm` and change it to this:

```html
<cfoutput>
<p>Hello #encodeForHTML( rc.name )#, today is #prc.when#</p>
</cfoutput>
```

Please note that we used the ColdFusion function `encodeForHTML()` (https://www.cfdocs.org/encodeforhtml) on the public variable. Why? Because you can never trust the client and what they send, make sure you use the built-in ColdFusion encoding function in order to avoid XSS hacks or worse on incoming public variables.

If you execute the event now: `http://localhost:{port}/hello/index` you will see a message of `Hello nobody`. Now change the incoming URL to this: `http://localhost:{port}/hello/index?name=ColdBox` and you will see a message of `Hello ColdBox`.
