# Default Module Layout

You can also implicitly setup a default layout to use when rendering views from the specific module. This only works when you call the *event.setView()* method from your module event handlers. Once called, the framework will discover the default layout to render that view in. You set up the default layout for a module by using the layoutSettings structure in your configuration:

```js
layoutSettings = {
  defaultLayout = "MyLayout.cfm"
};
```
