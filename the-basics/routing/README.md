# Routing

ColdBox sports a Routing Service that will provide you with robust URL mappings for building expressive applications and RESTFul services.  By convention URL routing will allow you to create URL's without using verobse parameter delimeters like `?event=this.that&m1=val`.

```javascript
// Old Style
http://localhost/index.cfm?event=home.about&page=2
http://localhost/index.cfm?city=24&page=3&county=234324324
```

```javascript
// New Routing Style
http://localhost/home/about/page/2
http://localhost/dade/miami/page/3
```

{% hint style="info" %}
If you are leveraging CommandBox as your server, then full URL rewrites are enabled by default.  This means you do not need a web server to remove the index.cfm from the URL.
{% endhint %}

## What is a route?

A route is a declared URL pattern that if matched it will translate such URL into one of the following:

* A ColdBox event
* A View/Layout
* A Reponse
* A Redirection

It will also inspect the URL for placeholders and translate them into the incoming Request Collection \(`rc`\).

**Example**

```javascript
route( "/blog/:year-numeric{4}/:month?/:day?" )
    .to( "blog.list" );
```

## Routing Benefits

There are several benefits that you will get by using our routing system:

* Complete control of how URL's are built and managed
* Ability to create or build URLs' dynamically
* Technology hiding
* Greater application portability
* URL's are more descriptive and easier to remember

