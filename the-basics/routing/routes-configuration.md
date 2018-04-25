# Application Router

Every ColdBox application has a URL router and can be located by convention at `config/Router.cfc`.  This is called the **application router** and it is based on the router core class: `coldbox.system.web.routing.Router`.  

{% hint style="info" %}
Please see the latest [API Docs](http://apidocs.ortussolutions.com/coldbox/5.0.0/coldbox/system/web/routing/Router.html) for investigating all the methods and properties of the Router.
{% endhint %}

{% hint style="success" %}
**Tip: **Unlike previous versions of ColdBox, the new routing services in ColdBox 5 are automatically configured to detect the base URLs and support multi-domain hosting. There is no more need to tell the Router about your base URL.
{% endhint %}

## Application Router - `Router.cfc`

{% code-tabs %}
{% code-tabs-item title="config/Router.cfc" %}
```javascript
component{

    function configure(){
        // Turn on Full URL Rewrites, no index.cfm in the URL
        setFullRewrites( true );
        
        // Routing by Convention
        route( "/:handler/:action?" ).end();
        
    }
    
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The application router is a simple CFC that virtually inherits from the core ColdBox Router class and is configured via the `configure()` method.  It will be decorated with all the capabilties to work with any request much like any event handler or interceptor.  In this router you will be doing 1 of 2 things:

1. Configuring the Router
2. Adding Routes via the Routing DSL

## Generated Settings

Once the routing service loads your Router it will create two application settings for you:

* `SESBaseURL` : The multi-domain URL base URL of your application: `http://localhost`
* `HTMLBaseURL` : The same path as `SESBaseURL`but without any `index.cfm` in it \(Just in case you are using `index.cfm` rewrite\). This is a setting used most likely by the HTML `<base>` tag.

## Configuration Methods

You can use the following methods to fine tune the configuration and operation of the routing services:

| **Method** | **Description** |
| --- | --- |
| `setEnabled( boolean )` | Enable/Disable routing, **enabled** by default |
| `setUniqueURLS( boolean )` | Enables SES only URL's with permanent redirects for non-ses urls. Default is **true**, but we highly encourage you to always use false to allow for dual types of URLs. If true and a URL is detected with ? or & then the application will do a 301 Permanent Redirect and try to translate the URL to a valid SES URL. |
| `setBaseURL( string )` | The base URL to use for URL writing and relocations. This is usally includes the web address to your application wether it is in the root or embedded in a folder. e.g. [http://www.coldbox.org/](http://www.coldbox.org/), [http://mysite.com/index.cfm](http://mysite.com/index.cfm)'' |
| `setLooseMatching( boolean )` | By default URL pattern matching starts at the beginning of the URL, however, you can choose loose matching so it searches anywhere in the URL. |
| `setExtensionDetection( boolean )` | By default ColdBox detects URL extensions like json, xml, html, pdf which can allow you to build awesome RESTful web services. |
| `setValidExtensions( list )` | Tell the interceptor what valid extensions your application can listen to. By default it listens to: _json, jsont, xml, cfm, cfml, html, htm, rss, pdf_ |
| `setThrowOnInvalidExtensions( boolean )` | By default ColdBox does not throw an exception when an invalid extension is detected. If true, then the interceptor will throw a 406 Invalid Requested Format Extension: {extension} exception. |
| `setAutoReload( boolean )` | By default all URL mapping routes are processed on application startup and cached. You can enable auto reloading of the routes in each request just in case your are in development and want to see change of URL mappings immediately. TURN TO FALSE IN PRODUCTION |
| `includeRoutes( path )` | Gives you the ability to include other routing configuration files. Great for separation and module SES rewriting. |

The next sections will discus how to register routes for your application.

