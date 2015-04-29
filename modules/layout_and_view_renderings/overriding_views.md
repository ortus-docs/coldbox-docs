# Overriding Views

This tells the framework to render the `simple/index.cfm` view in the module `simpleModule`. However, let's override the view first. Go to the host application's views folder and create a folder called `modules` and then a folder according to the module name, in our case `simpleModule`:

```js
/MyApp
  + views 
    + modules 
       + simpleModule
```

What this does, is create the override structure that you need. So now, you can map 1-1 directly from this overrides folder to the module's views folder. So whatever view you place here that matches 1-1 to the module, the parent will take precedence. Now remember this is because our *viewParentLookup* property is set to true. If we disable it or set it to false, then the module takes precedence first. If not found in the module, then it goes to the parent or host application and tries to locate the view. That's it, so if I place a *simple/index.cfm* view in my parent structure, the that one will take precedence.