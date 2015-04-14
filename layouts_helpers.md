# Layouts Helpers

This is a nifty little feature that enables you to create nice helper templates on a per-layout or per-layout-folder basis. If the framework detects the helper, it will inject it into the rendering layout so you can use methods, properties or whatever. All you need to do is follow a set of conventions. Let's say we have a layout in the following location:

```js
/layouts
  /general
    +main.cfm
```

Then we can create the following templates
* *mainHelper.cfm* : Helper for the main.cfm layout.
* *generalHelper.cfm* : Helper for any layout in the general folder.

