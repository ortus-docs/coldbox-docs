# AppMapping

##### AppMapping
In your Application.cfc you will find a directive called: COLDBOX_APP_MAPPING:

```js
<cfset COLDBOX_APP_MAPPING   = "">
```
This tells the framework where in the web server (sub-folder) your application is located in. By default, your application is the root of your website so this value is empty or / and you won't modify this. But if your application is in a sub-folder then add the full instantation path here. So if your application is under */apps/myApp* then the value would be:

```js
<cfset COLDBOX_APP_MAPPING   = "apps.myApp">
```

> **Infor** The AppMapping value is an instantiation path value