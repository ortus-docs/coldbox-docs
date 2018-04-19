# Implicit Views

You can also omit the explicit `event.setView()` if you want, ColdBox will then look for the view according to the executing event's syntax. So if the incoming event is called `general.index` and no view is explicitly defined in your handler, ColdBox will look for a view in the `general` folder called `index.cfm`. That is why we recommend trying to match event resolution to view resolution even if you use or not implicit views. This feature is more for conventions purists than anything else. However, we do recommend as best practice to use explicitly declare the view to be rendered when working with team environments.

```javascript
component name="general"{

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();    
    }

}
```

> **Caution** If using implicit views, please note that the name of the view will ALWAYS be in lower case. So please be aware of this limitation. I would suggest creating SES URL Mappings with explicit event declarations so case and location can be controlled. When using implicit views you will also loose fine rendering control.
>
> ## Disabling Implicit Views
>
> You can also disable implicit views by using the `coldbox.implicitViews` boolean setting in your `ColdBox.cfc`. This is useful as implicit lookups are time-consuming.

```javascript
coldbox.implicitViews = true;
```

## Case Sensitivity

The ColdBox rendering engine can also be tweaked to use case-insensitive or sensitive implicit views by using the `coldbox.caseSensitiveImplicitViews` directive in your `ColdBox.cfc`. The default is to turn all implicit views to lower case, so the value is always **false**.

```javascript
coldbox.caseSensitiveImplicitViews = true
```

