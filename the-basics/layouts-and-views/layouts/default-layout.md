# Default Layout

## Default Layout

The default layout in a ColdBox application is `layouts/main.cfm` by convention. You can change this by using the `layoutSettings` in your `Configuration.cfc`.

```javascript
//Layout Settings
layoutSettings = {
    defaultLayout = "basic.cfm"
}
```

## Default View

There is no default view in ColdBox, but you can configure one by using the same `layoutSettings` configuration directive and the `defaultView` directive:

```javascript
//Layout Settings
layoutSettings = {
    defaultLayout = "basic.cfm",
    defaultView   = "noview.cfm"
}
```

This means that if no view is set in the request context, ColdBox will fallback to this view for rendering.

