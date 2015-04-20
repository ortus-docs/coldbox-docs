# Default Layout

All ColdBox applications give you the capability of choosing a default layout that can be used to render all your views in. This is done by choosing that default layout in your [Configuration CFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) in the layoutSettings structure:

```js
//Layout Settings
layoutSettings = {
	defaultLayout = "basic.cfm"
}
```

With this little configuration snippet, all set views or main views that do not define a layout will use the default one by convention.

#### Default View
The Default View element is a very cool feature of the layout manager. This element allows you to declare the name of the view to render if the event handler does not set a view for rendering.

```js
//Layout Settings
layoutSettings = {
	defaultLayout = "basic.cfm",
	defaultView   = "noview.cfm"
}
```

