# Requirements

Routing is enabled by **default** in the ColdBox application templates in order to work with URL's like this:

`http://localhost/index.cfm/home/about`

As you can see they still contain the `index.cfm` in the URL. In order to enable full URL rewrites that eliminates that `index.cfm` you must have a rewrite enabled webserver like Apache, nginx or IIS or a Java rewrite filter which ships with CommandBox by default.

`http://localhost/home/about`

CommandBox has built in rewrites powered by Tuckey and you can enable a server with rewrites by running:

```text
server start --rewritesEnable
```

{% hint style="danger" %}
**Caution** Some J2EE servlet containers do not support the forwarding of SES parameters via the routing template \(`index.cfm`\) out of the box. You might need to enable full URL rewriting either through a web server or a J2EE filter.
{% endhint %}

## Some Resources

* [Apache mod\_rewrite](http://httpd.apache.org/docs/current/mod/mod_rewrite.html) via .htaccess or configuration files \(Free\)
* [Helicon Tech](http://www.helicontech.com/) ISAPI rewrite filter for IIS \(Paid\)
* [IIS 7](http://www.iis.net/downloads/microsoft/url-rewrite) native rewrite filter \(Free\)
* [nginx](http://nginx.org/) native web server \(free\)
* [Tuckey](http://www.tuckey.org/) J2EE rewrite filter \(free\)

