# Rewrite Rules

Here are just a few of those rewrite rules for you for major rewrite engines. You can spice them up as needed.

## .htaccess

```javascript
RewriteEngine on
#if this call related to adminstrators or non rewrite folders, you can add more here.
RewriteCond %{REQUEST_URI} ^/(.*(CFIDE|cfide|CFFormGateway|jrunscripts|railo-context|lucee|mapping-tag|fckeditor)).*$
RewriteRule ^(.*)$ - [NC,L]

#Images, css, javascript and docs, add your own extensions if needed.
RewriteCond %{REQUEST_URI} \.(bmp|gif|jpe?g|png|css|js|txt|xls|ico|swf)$
RewriteRule ^(.*)$ - [NC,L]

#The ColdBox index.cfm/{path_info} rules.
RewriteRule ^$ index.cfm [QSA,NS]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.cfm?%{REQUEST_URI} [QSA,L,NS]
```

{% hint style="warning" %}
The above htaccess file might not work combined with Apache. Recent versions of Apache don't send the CGI.PATH\_INFO variable to your cfml engine when using ProxyPass and ProxyPassMatch. It that's the case you might need a [pathInfoProvider](../pathinfo-providers.md) function in your router.cfc
{% endhint %}

The following solution might work better if you are using a recent version of Apache. This should be part of your `.htaccess` file

```text
#The ColdBox index.cfm/{path_info} rules.
RewriteEngine On
RewriteRule ^$ /index.cfm?redirect_path=/ [QSA,NS]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /index.cfm?redirect_path=%{REQUEST_URI} [QSA,L,NS]
```

Now you can create a pathInfo provider function in your router.cfc which brings back your path info to the router:

{% code title="router.cfc" %}
```javascript
function PathInfoProvider( event ){
  var p = cgi.path_info;
  if (len(p)) {
    return p;
  } else if (url.keyExists("redirect_path")) {
    return url.redirect_path;
  } else {
    return "";
  }
}
```
{% endcode %}

## nginx

```text
################### LOCATION: ROOT #####################
location / {
     # First attempt to serve real files or directory, else it sends it to the @rewrite location for processing
     try_files $uri $uri/ @rewrite;
}

################### @REWRITE: COLDBOX SES RULES #####################
# Rewrite for ColdBox (only needed if you want SES urls with this framework)
# If you don't use SES urls you could do something like this
# location ~ \.(cfm|cfml|cfc)(.*)$ {
location @rewrite {
  rewrite ^/(.*)? /index.cfm/$request_uri last;
  rewrite ^ /index.cfm last;
}

################### CFM/CFC LUCEE HANDLER #####################
# The above locations will just redirect or try to serve cfml files
# We need this to tell NGinx that if we receive the following requests to pass them to Lucee
location ~ \.(cfm|cfml|cfc|jsp)(.*)$ {
# Include our connector
include lucee.conf;
}
```

## IIS7 web.config

Please note that URL rewriting is handled by an optional module in IIS. More info here: [https://www.iis.net/downloads/microsoft/url-rewrite](https://www.iis.net/downloads/microsoft/url-rewrite)

```javascript
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
               <rule name="Application Administration" stopProcessing="true">
                    <match url="^(.*)$" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{SCRIPT_NAME}" pattern="^/(.*(CFIDE|cfide|CFFormGateway|jrunscripts|lucee|railo-context|fckeditor)).*$" ignoreCase="false" />
                    </conditions>
                    <action type="None" />
                </rule>
                <rule name="Flash and Flex Communication" stopProcessing="true">
                    <match url="^(.*)$" ignoreCase="false" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{SCRIPT_NAME}" pattern="^/(.*(flashservices|flex2gateway|flex-remoting)).*$" ignoreCase="false" />
                    </conditions>
                    <action type="Rewrite" url="index.cfm/{PATH_INFO}" appendQueryString="true" />
                </rule>
                <rule name="Static Files" stopProcessing="true">
                    <match url="^(.*)$" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{SCRIPT_NAME}" pattern="\.(bmp|gif|jpe?g|png|css|js|txt|pdf|doc|xls)$" ignoreCase="false" />
                    </conditions>
                    <action type="None" />
                </rule>
                <rule name="Insert index.cfm" stopProcessing="true">
                    <match url="^(.*)$" ignoreCase="false" />
                    <conditions logicalGrouping="MatchAll">
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="index.cfm/{PATH_INFO}" appendQueryString="true" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>
```

## Tuckey Rewrite

```text
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE urlrewrite PUBLIC "-//tuckey.org//DTD UrlRewrite 3.2//EN" "http://tuckey.org/res/dtds/urlrewrite3.2.dtd">
<urlrewrite>
    <rule>
        <note>ContentBox Media URLs</note>
        <condition type="request-uri" operator="equal">^/__media/.*$</condition>
        <from>^/(.*)$</from>
        <to type="passthrough">/index.cfm/$1</to>
    </rule>
    <rule>
        <note>Generic Front-Controller URLs</note>
        <condition type="request-uri" operator="notequal">/(index.cfm|robots.txt|osd.xml|flex2gateway|cfide|cfformgateway|railo-context|lucee|admin-context|modules/contentbox-dsncreator|modules/contentbox-installer|modules/contentbox|files|images|js|javascripts|css|styles|config).*</condition>
        <condition type="request-uri" operator="notequal">\.(bmp|gif|jpe?g|png|css|js|txt|xls|ico|swf|woff|ttf|otf)$</condition>
        <condition type="request-filename" operator="notdir"/>
        <condition type="request-filename" operator="notfile"/>
        <from>^/(.+)$</from>
        <to type="passthrough">/index.cfm/$1</to>
    </rule>
</urlrewrite>
```

