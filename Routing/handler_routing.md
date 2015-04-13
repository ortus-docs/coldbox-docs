# Handler Routing

Once you declare a route you will most likely route it to an event by using the handler and/or action arguments:

```js
addRoute(pattern="/blog", handler="blog", action="index");
```
This create the *event=blog*.index translation for you.