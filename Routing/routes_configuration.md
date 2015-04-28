# Routes Configuration

The `SES` interceptor is the class in ColdBox that provides you with URL Routing and RESTful support.  It is enabled by default in all application templates in your `ColdBox.cfc` configuration file:

```js
interceptors = [
    { class="coldbox.system.interceptors.SES" }
]
```

By convention the interceptor will look for `config/Routes.cfm` as your configuration file. If you want to change this, then declare a property of the interceptor with a path to your configuration file:

```js
interceptors = [
    {
        class="coldbox.system.interceptors.SES",
        properties = { configFile = "myconfig/path/Routes.cfm" } 
    }
];
```

## Generated Settings
Once the SES interceptor loads in your application it will create two settings for you inside of the ColdBox Controller:

* `SESBaseURL` : The location path to your application that will be setup in your `Routes.cfm`
* `HTMLBaseURL` : The same path as `SESBaseUR`L but without any `index.cfm` in it (Just in case you are using `index.cfm` rewrite). This is a setting used most likely by the HTML `<base>` tag.


Inside of your `Routes.cfm` template is where you will use our routing DSL (Domain Specific Language) to define configuration parameters for your routing, RESTful URIs and to create URL mappings.

> **Caution** The routes configuration file gets executed within the interceptor, so ALL interceptor methods are available for your usage. You can use tier detection, settings, etc. 

## Configuration Methods

You can use the following methods to fine tune the configuration and operation:

|Method|Description|
|--|--|
| `setEnabled( boolean )` | Enable/Disable routing, enabled by default |
| `setUniqueURLS( boolean )` |Enables SES only URL's with permanent redirects for non-ses urls. Default is true, but we highly encourage you to always use false to allow for dual types of URLs. If true and a URL is detected with ? or & then the application will do a 301 Permanent Redirect and try to translate the URL to a valid SES URL.|
| `setBaseURL( string )` | The base URL to use for URL writing and relocations. This is usally includes the web address to your application wether it is in the root or embedded in a folder. e.g. http://www.coldbox.org/, http://mysite.com/index.cfm''|
| `setLooseMatching( boolean )` | By default URL pattern matching starts at the beginning of the URL, however, you can choose loose matching so it searches anywhere in the URL.|
| `setExtensionDetection( boolean )` | By default ColdBox detects URL extensions like json, xml, html, pdf which can allow you to build awesome RESTful web services.|
| `setValidExtensions( list )` | Tell the interceptor what valid extensions your application can listen to. By default it listens to: *json, jsont, xml, cfm, cfml, html, htm, rss, pdf*|
| `setThrowOnInvalidExtensions( boolean )` | By default ColdBox does not throw an exception when an invalid extension is detected. If true, then the interceptor will throw a 406 Invalid Requested Format Extension: {extension} exception.|
| `setAutoReload( boolean )` | By default all URL mapping routes are processed on application startup and cached. You can enable auto reloading of the routes in each request just in case your are in development and want to see change of URL mappings immediately. TURN TO FALSE IN PRODUCTION|
| `includeRoutes( path )` |Gives you the ability to include other routing configuration files. Great for separation and module SES rewriting.|


## Base URL Per Request
If you are running your sites on a multi-domain configuration or a multi-tenant application, then you will need to advice the request context of the current routed base URL in order for the SES interceptor to route correctly.  Below is a simple intercepting listener you can place in your `Coldbox.cfc`:

```js
/**
* Multi-domain hosting
*/
function preProcess( event ){
	// find appmaping
	var appMapping = ( len( controller.getSetting( 'AppMapping' ) ) ? controller.getSetting( 'AppMapping' ) & "/" : "" );
	// Setup base URL
	event.setSESBaseURL( "http" & ( event.isSSL() ? "s" : "" ) & "://#cgi.HTTP_HOST#/#appMapping#" );
}
```

