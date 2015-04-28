# Requirements



> **Caution**  Some J2EE servlet containers do not support the forwarding of SES parameters via the routing template (`index.cfm`) out of the box. You might need to enable full URL rewriting either through a web server or a J2EE filter. 

If you do not use full URL rewrites, the URL might look like this:

`http://localhost/index.cfm/home/about`

The following is with full URL enabled, which eliminates the `index.cfm`:

`http://localhost/home/about`

## Some Resources

* [Apache mod_rewrite](http://httpd.apache.org/docs/current/mod/mod_rewrite.html) via .htaccess or configuration files (Free)
* [Helicon Tech](http://www.helicontech.com/) ISAPI rewrite filter for IIS (Paid)
* [IIS 7](http://www.iis.net/downloads/microsoft/url-rewrite) native rewrite filter (Free)
* [nginx](http://nginx.org/) native web server (free)
* [Tuckey](http://www.tuckey.org/) J2EE rewrite filter (free)


