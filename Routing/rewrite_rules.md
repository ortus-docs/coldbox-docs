# Rewrite Rules

Here are just a few of those rewrite rules for you for major rewrite engines. You can spice them up as needed.

## .htaccess

```js
RewriteEngine On
RepeatLimit 0

#dealing with cf-administrator,railo-administrator, etc
RewriteRule ^/(CFIDE|cfide|CFFormGateway|jrunscripts|lucee|railo-context|fckeditor) - [L,I]

#dealing with flash / flex communication, cf-administrator,railo-administrator, etc
RewriteRule ^/(flashservices|flex2gateway|flex-remoting) - [L,I]

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.cfm/%{REQUEST_URI} [QSA,L]
```

## IIS7 web.config

```js
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
               <rule name="Application Adminsitration" stopProcessing="true">
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

## SES Interceptor

The SES interceptor is the class in ColdBox that provides you with URL Mapping and RESTful support. You will have this declared (or need to declare) in your [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm):


```js
interceptors = [
  {class="coldbox.system.interceptors.SES"}
];
```

By convention the interceptor will look for *config/Routes.cfm* as your configuration file. If you want to change this, then declare a property of the interceptor with a path to your configuration file:

```js
interceptors = [
  {class="coldbox.system.interceptors.SES",
   properties = {configFile = "myconfig/path/Routes.cfm"} }
];
```

Once the SES interceptor loads in your application it will create two settings for you:

* SESBaseURL : The location path to your application that will be setup in your *Routes.cfm*
* HTMLBaseURL : The same path as SESBaseURL but without any *index.cfm *in it (Just in case you are using index.cfm rewrite). This is a setting used most likely by the HTML <base> tag.
