# Pattern Placeholders

### Alphanumeric Placeholders

In your URL pattern you can also use the `:` syntax to denote a **variable placeholder**. These position holders are **alphanumeric** by default:

```javascript
route( "blog/:year/:month?/:day?", "blog.index" );
```

Once a URL is matched to the route above, the placeholders \(`:year/:month?/:day?`\) will become request collection \(`RC`\) variables:

```text
http://localhost/blog/2012/12/22 -> rc.year=2012, rc.month=12, rc.day=22
http://localhost/blog/2012/12-> rc.year=2012, rc.month=12
http://localhost/blog/2012-> rc.year=2012
```

### Optional Placeholders

Sometimes we will want to declare routes that are very similar in nature and since order matters, they need to be delcared in the right order.  Like this one:

```java
route( "/blog/:year-numeric/:month-numeric/:day-numeric" );
route( "/blog/:year-numeric/:month-numeric" );
route( "/blog/:year-numeric/" );
route( "/blog/" );
```

However, we just wrote 4 routes for this approach when we can just use optional variables by using the `?` symbol at the end of the placeholder. This tells the processor to create the routes for you in the most detailed manner first instead of you doing it manually.

```java
route( "/blog/:year-numeric?/:month-numeric?/:day-numeric?" );
```

{% hint style="danger" %}
**Caution** Just remember that an optional placeholder cannot be followed by a non-optional one. It doesn't make sense.
{% endhint %}

### Numeric Placeholders

ColdBox gives you also the ability to declare numerical only routes by appending `-numeric` to the variable placeholder so the route will only match if the placeholder is **numeric**. Let's modify the route from above.

```javascript
route( "blog/:year-numeric/:month-numeric?/:day-numeric?", "blog.index" );
```

This route will only accept years, months and days as numbers.

### Alpha Placeholders

ColdBox gives you also the ability to declare alpha only routes by appending `-alpha` to the variable placeholder so the route will only match if the placeholder is `alpha` only.

```javascript
route( "wiki/:page-alpha", "wiki.show" );
```

This route will only accep page names that are alpha only.

There are two ways to place a regex constraint on a placeholder, using the `-regex:` placeholder or adding a `constraints` structure to the route declaration.

### Regular Expression Placeholders

You can also have the ability to declare a placeholder that must match a regular expression by using the `-regex( {regex_here} )` placeholder.

```javascript
// route with regex placeholders
route(
    pattern="/api/:format-regex:(xml|json)/",
    target="api.execute"
);
```

The `rc` variable `format` must match the regex supplied: `(xml|json)`

### Regular Expression Constraints

You can also apply a structure of regular expressions to a route instead of inlining the regular expressions in the placeholder location.  You will do this using the `constraints()` method of the router.

The **key** in the structure must match the **name** of the placeholder and the **value** is a regex expression that must be enclosed by parenthesis `()`.

```javascript
// route with custom constraints
route(
    pattern = "/api/:format/:entryID",
    target  = "api.execute"
).constraints( {
    format  = "(xml|json)",
    entryID = "([0-9]{4})" 
} );
```



