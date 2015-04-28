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

## nginx

```
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

################### CFM/CFC RAILO HANDLER #####################
# The above locations will just redirect or try to serve cfml files
# We need this to tell NGinx that if we receive the following requests to pass them to Railo
location ~ \.(cfm|cfml|cfc|jsp)(.*)$ {
# Include our connector
include railo.conf;
}

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