# Optional Placeholders

Most of the time we need to create several routes in order to determine possible routings and they must be declared in a specific order so they match. A great example is the declaration of the following:

```js
/blog/:year-numeric/:month-numeric/:day-numeric
/blog/:year-numeric/:month-numeric
/blog/:year-numeric/
/blog/
```

However, we just wrote 4 routes for this when we can just use optional variables by using the `?` symbol at the end of the placeholder. This tells the processor to create the routes for you in the most detailed manner first:

```js
/blog/:year-numeric?/:month-numeric?/:day-numeric?
```

> **Info** Just remember that an optional placeholder cannot be followed by a non-optional one. It doesn't make sense.


