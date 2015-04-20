# RESTful Action Routing

You can also use a structure for the action argument so you can split the incoming HTTP method verbs into the appropriate actions:

```js
addRoute(pattern="/user/:username?", handler="users", action={
  GET = "list", POST = "create", PUT = "save", DELETE = "remove", HEAD ="info"
});
```

Isn't that cool! Just like that you can split the incoming URL pattern to appropriate action executions according to the incoming HTTP verb.

