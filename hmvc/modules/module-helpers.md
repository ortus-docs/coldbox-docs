# Module Helpers

Every module can declare an array of helper templates that contain **methods** that will be added as **global** application helpers.  This is a great way to collaborate global functions to the running MVC application.

This is done via the `this.applicationHelper` directive in your `ModuleConfig.cfc`.  This is an array of relative paths in the module that ColdBox will load as mixins.

```javascript
this.applicationHelper = [
    "includes/helpers.cfm"
];
```

Here is a sample of a `helpers.cfm`:

```javascript
<cfscript>
    function auth() {
        return wirebox.getInstance( "AuthenticationService@cbauth" );
    }
</cfscript>
```



