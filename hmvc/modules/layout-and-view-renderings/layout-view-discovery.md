# Layout\/View Discovery

The default order of overrides ColdBox offers is both `viewParentLookup & layoutParentLookup` to **true**. This means that if the layout or view requested to be rendered by a module exists in the overrides section of the host application, then the host application's layout or view will be rendered instead. Let's investigate the order of discover:

![](../../../.gitbook/assets/modulesviewlookuptrue.jpg)

![](../../../.gitbook/assets/modulesviewlookupfalse.jpg)

**viewParentLookup = true**

1. Host override module specific `(e.g. {HOST}/views/modules/myModule/myView.cfm)`
2. Host override common `(e.g. {HOST}/views/modules/myView.cfm)`
3. Module view `(e.g. /modules/myModule/views/myView.cfm)`
4. Default view discovery from host `(e.g. {HOST}/views/myView.cfm)`

**viewParentLookup = false**

1. Module view `(e.g. /modules/myModule/views/myView.cfm)`
2. Host override module specific `(e.g. {HOST}/views/modules/myModule/myView.cfm)`
3. Host override common `(e.g. {HOST}/views/modules/myView.cfm)`
4. Default view discovery from host `(e.g. {HOST}/views/myView.cfm)`

![](../../../.gitbook/assets/moduleslayoutlookuptrue.jpg)

![](../../../.gitbook/assets/moduleslayoutlookupfalse.jpg)

**layoutParentLookup = true**

1. Host override module specific `(e.g. {HOST}/layouts/modules/myModule/myLayout.cfm)`
2. Host override common `(e.g. {HOST}/layouts/modules/myLayout.cfm)`
3. Module layout `(e.g. /modules/myModule/layouts/myLayout.cfm)`
4. Default layout discovery from host `(e.g. {HOST}/layouts/Default.cfm)`

**layoutParentLookup = false** 1. Module layout `(e.g. /modules/myModule/layouts/myLayouts.cfm)` 2. Host override module specific `(e.g. {HOST}/layouts/modules/myModule/myLayout.cfm)` 3. Host override common `(e.g. {HOST}/layouts/modules/myLayout.cfm)` 4. Default layout discovery from host `(e.g. {HOST}/layouts/Default.cfm)`

Let's do some real examples, I am building a simple module with 1 layout and 1 view. Here is my directory structure for them:

```javascript
/simpleModule
  + layouts
    + main.cfm
  + views
    + simple
       + index.cfm
```

Now, in my handler code I just want to render the view by using our typical `event.setView()` method calls or implicit views.

```javascript
// In a handler called simple.cfc
component{

  function index(event){
      // DO SOME CODE HERE

        // Set the view to render with a layout
      event.setView(view='simple/index',layout="main");
    }
}
```

