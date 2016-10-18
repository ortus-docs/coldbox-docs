# Adding Event Handlers

We have now seen how to add handlers via CommandBox using the `coldbox create handler` command and also execute them by convention by leveraging the following URL pattern:

```
http://localhost:{port}/folder/handler/action
http://localhost:{port}/handler/action
http://localhost:{port}/handler
```

Also remember, that if no `action` is defined in the incoming URL then the default action of `index` will be used.  

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

