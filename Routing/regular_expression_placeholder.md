# Regular Expression Placeholder

There are two ways to place a regex constraint on a placeholder, using the regex: placeholder or adding a constraints structure to the route declaration.

#### regex()

```js
// route with regex constraint
addRoute(pattern="/api/regex:(xml|json)/",
         handler="api",
         action="execute");
```

> **Info** If you use the regex() DSL then no variable will be created in the request collection. Only the regex is evaluated in the placeholder

#### constraints

The key in the structure must match the name of the placeholder and the value is a regex expression that must be enclosed by parenthesis ()'.

```js
// route with custom constraints
addRoute(pattern="/api/:format/",
     handler="api",
     action="execute",
     contraints={
      format = "(xml|json)"
     });
```

     