# ColdBox URL Routing

ColdBox URL Routing will give you support for creating Search Engine Safe (SES) URLs, RESTful services, and overall URL routing your way.  By convention URL routing  will allow you to create URL's without using nasty parameter delimeters like `?event=this.that&m1=val`.

```js
// Old Style
http://localhost/index.cfm?event=home.about&page=2
http://localhost/index.cfm?city=24&page=3&county=234324324
```

You can have the same event URL but with a nicer routing interface:

```js
// Routing Style
http://localhost/home/about/page/2
http://localhost/dade/miami/page/3
```

## What is a route?

A route is a declared URL pattern that if matched it will translate such URL into either an event or a view to be dispatched with the appropriate variable assignments in the request collection.  Example: `blog/:year/:month?/:day?`.

## Routing Benefits

There are several benefits that you will get by using our routing system:

* Complete control of how URL's are built and maintained
* Ability to create or build URLs' dynamically
* Technology hiding
* Greater application portability
* URL's are more descriptive and easier to remember


