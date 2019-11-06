# Routing

ColdBox supports a Routing Service that will provide you with robust URL mappings for building expressive applications and RESTFul services. By convention URL routing will allow you to create URL's without using verbose parameter delimiters like `?event=this.that&m1=val` and execute ColdBox events.

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
If you are leveraging CommandBox as your server, then full URL rewrites are enabled by **default**. This means you do not need a web server to remove the `index.cfm` from the URL.
{% endhint %}

## What is a route?

A route is a declared URL pattern that if matched it will translate the URL into one of the following:

* A ColdBox event to execute
* A View/Layout to render
* A Reponse function to execute
* A Redirection to occur

It will also inspect the URL for **placeholders** and translate them into the incoming Request Collection variables \(`RC`\).

**Examples**

{% code-tabs %}
{% code-tabs-item title="config/Router.cfc" %}
```javascript
function configure(){

    // Routing with placeholders to an event with placeholders
    route( "/blog/:year-numeric{4}/:month?/:day?" )
        .to( "blog.list" );

    // Redirects
    route( "/old/book" )
        .redirect( "/mybook" );

    // Responses
    route( "/echo" ).toResponse( (event,rc,prc) => {
        return "hello luis";
    } );

    // Shortcut to above
    route( "/echo", (event,rc,prc) => {
        return "hello luis";
    } );

    // Show view
    route( "/contact-us" )
        .toView( "main/contact" );

    // Direct to handler with action from URL
    route( "/users/:action" )
        .toHandler( "users" );

    // Inline pattern + target and name
    route( pattern="/wiki/:page", target="wiki.show", name="wikipage" );

}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Routing Benefits

There are several benefits that you will get by using our routing system:

* Complete control of how URL's are built and managed
* Ability to create or build URLs' dynamically
* Technology hiding
* Greater application portability
* URL's are more descriptive and easier to remember

## Route Visualizer

As you create route-heavy applications visualizing the routes will be challenging especially for HMVC apps with lots of modules. Just install our [ColdBox Route Visualizer](https://www.forgebox.io/view/route-visualizer) and you will be able to visually see, test and debug all your routing needs.

```bash
box install route-visualizer
```

