## Event Handlers

<img src="/images/ColdBoxSimpleMVC.png">

Event handlers are the controller layer in ColdBox and is what you will be executing via the URL or a FORM post. All event handlers are **singletons**, which means they are cached for the duration of the application, so always remember to var scope your variables in your functions. 

Once you started the server in the previous section and opened the browser, the default event got executed which maps to an event handler CFC (controller) `handlers/main.cfc` and the method/action in that CFC alled `index()`. Go open the `handler/main.cfc` and explore the code.


```js
// Default Action
function index( event, rc, prc ){
    prc.welcomeMessage = "Welcome to ColdBox!";
    event.setView( "main/index" );
}
```

Every action in ColdBox receives three arguments:

* `event` - An object that models and is used to work with the current request
* `rc` - A struct that contains both URL/FORM variables (unsafe data)
* `prc` - A secondary struct that is private only settable from within your application (safe data)

This line `event.setView( "main/index" )` told ColdBox to render a view back to the user found in `views/main/index.cfm` using a default layout, which by convention is called `Main.cfm` which can be found in the `layouts` folder.


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
