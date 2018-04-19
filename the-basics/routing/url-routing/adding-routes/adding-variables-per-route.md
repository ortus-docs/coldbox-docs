# Adding variables per route

You can add variables to the request collection by using the `matchVariables` argument or by adding your extra name-value pairs to the method as a-la-carte arguments. The `matchVariables` argument is a list of name-value pairs that you can pass to the route definition. This basically tells the processor that if that specific route matches, then add those name-value pairs to the request collection.

```javascript
addRoute(
    pattern            = "space/:space",
    handler            = "page",
    action            = "show",
    matchVariables    = "spaceUsed=true,useNavigation=false"
);
```

As mentioned above, you can also add variables by just adding them as arguments to the _addRoute\(\)_ method:

```javascript
addRoute( 
    pattern            = "space/:space",
    handler            = "page",
    action            = "show",
    spaceUsed        = true,
    useNavigation    = false
);
```

