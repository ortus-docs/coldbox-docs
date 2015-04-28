# View Routing

You can also route a URL pattern to a view with its default or assigned layout or none at all.  

```js
addRoute( pattern="/contact-us", view="static/contact");
addRoute( pattern="/newsletter", view="static/newsletter", noLayout=true );
```

> **Info** Remember that no event will be executed.