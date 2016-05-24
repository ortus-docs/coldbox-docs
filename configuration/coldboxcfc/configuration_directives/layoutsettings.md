# `LayoutSettings`

This structure allows you to define a system-wide default layout and view.

```js
//Layout Settings
layoutSettings = {
    // The default layout to use if NOT defined in a request
	defaultLayout = "Main.cfm",
	// The default view to use to render if NOT defined in a request
	defaultView   = "youForgot.cfm"
};
```

> **Hint** Please remember that the default layout is `Main.cfm`
