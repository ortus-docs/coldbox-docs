# Handler Routing

Once you declare a route you will most likely route it to an event by using the `handler` and/or `action` arguments:

```javascript
addRoute(pattern="/blog", handler="blog", action="index");
```

This will translate the `/blog` URL pattern into `event=blog.index` for you.

