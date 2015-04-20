# Routing

ColdBox URL Mappings will give you support for creating Search Engine Safe (SES) URLs, RESTful services, and overall URL routing your way. We would love to take credit for this feature, but it was inspired by Rails and our good friend Adam Fortuna's ColdCourse project. By convention URL mapping support will allow you to create URL's without using the *?event=this.that¶m1=val* formats by more like the following example:

```js
// Old Style
http://localhost/index.cfm?event=home.about&page=2
http://localhost/index.cfm?city=24&page=3&county=234324324
```

You can have the same event but with a URL like this:

```js
// Routing Style
http://localhost/home/about/page/2
http://localhost/dade/miami/page/3
```

### What is a route?
> A route is a declared URL pattern that if matched it will translate such URL into either an event or a view to be dispatched. 

###Benefits

There are several benefits that you will get by using our routing system:

* Complete control of how URL's are built and maintained
* Ability to create or build URLs' dynamically
* Technology hiding
* Greater application portability
* URL's are more descriptive and easier to remember


