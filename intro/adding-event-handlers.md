# Adding Event Handlers

We have now seen how to add handlers via CommandBox and execute them by convention by leveraging the following URL pattern:


```
http://localhost:{port}/folder/handler/action
http://localhost:{port}/handler/action
http://localhost:{port}/handler
```

Also remember, that if no `action` is defined in the incoming URL then the default action of `index` will be used.  Now, let's open the handler we created before called 
