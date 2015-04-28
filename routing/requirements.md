# Requirements

Routing is provided by our SES interceptor and it is enabled by default in all application templates in your `ColdBox.cfc` configuration file:

```
interceptors = [
    { class="coldbox.system.interceptors.SES" }
]
```

> **Important**  Some J2EE servlet containers do not support the forwarding of SES parameters via the routing template out of the box. You might need to enable full URL rewriting either through a web server or a J2EE filter. 

`http://localhost/index.cfm/home/about`

`http://localhost/home/about`

Then you will need to enable URL rewriting at the web server level or use a J2EE rewrite filter. The most common are listed below:

## Some Resources

* [Apache mod_rewrite](http://httpd.apache.org/docs/current/mod/mod_rewrite.html) via .htaccess or configuration files (Free)
* [Helicon Tech](http://www.helicontech.com/) ISAPI rewrite filter for IIS (Paid)
* [IIS 7](http://www.iis.net/downloads/microsoft/url-rewrite) native rewrite filter (Free)
* [nginx](http://nginx.org/) native web server (free)
* [Tuckey](http://www.tuckey.org/) J2EE rewrite filter (free)


