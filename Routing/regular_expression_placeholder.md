# Regular Expression Placeholder

There are two ways to place a regex constraint on a placeholder, using the `-regex:` placeholder or adding a `constraints` structure to the route declaration.

## `:regex`

```js
// route with regex constraint
addRoute(
    pattern="/api/:format-regex:(xml|json)/",
    handler="api",
    action="execute"
);
```

The rc variable `format` must match the regex supplied: `(xml|json)`


## `constraints`

The key in the structure must match the name of the placeholder and the value is a regex expression that must be enclosed by parenthesis `()`.

```js
// route with custom constraints
addRoute(pattern="/api/:format/",
     handler="api",
     action="execute",
     contraints={
      format = "(xml|json)"
     });
```

     