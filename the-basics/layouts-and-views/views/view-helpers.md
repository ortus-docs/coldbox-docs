# View Helpers

This is a nifty little feature that enables you to create nice helper templates on a per-view, per-folder and per-application basis. If the framework detects the helper, it will inject it into the rendering view so you can use methods, properties or whatever. All you need to do is follow a set of conventions. Let's say we have a view in the following location:

```javascript
/views
  /general
    +home.cfm
```

Then we can create the following templates

* `homeHelper.cfm` : Helper for the home.cfm view.
* `generalHelper.cfm` : Helper for any view in the general folder.

```javascript
/views
  /general
    +home.cfm
    +homeHelper.cfm
    +generalHelper.cfm
```

**homeHelper.cfm**

```javascript
<cfscript>
// facade to get i18n resource
function $r(){ return getResource(argumentCollection=arguments); }
// cool formatted date function
function today(){ return dateFormat(now(),"full"); }
</cfscript>
```

That's it. Just append Helper to the view or folder name and there you go, the framework will use it as a helper for that view specifically. What can you put in these helper templates:

* NO BUSINESS CODE
* UI logic functions or properties
* Helper functions or properties
* Dynamic JavaScript or CSS

> **Hint** External views can also use our helper conventions

## Application wide helpers

You can also use the `coldbox.viewsHelper` directive to tell the framework what helper file to use for ALL views and layouts rendered:

```text
coldbox.viewsHelper = "includes/helpers/viewsHelper.cfm;
```

