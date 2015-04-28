# Overriding Layouts

Now, let's say you want to override the layout for a module. Go to the host application's layouts folder and create a folder called modules and then a folder according to the module name, in our case simpleModule:
```js
/MyApp
  + layouts 
    + modules 
       + simpleModule
```

What this does, is create the override structure that you need. So now, you can map 1-1 directly from this overrides folder to the module's layouts folder. So whatever layout you place here that matches 1-1 to the module, the parent will take precedence. Now remember this is because our layouParentLookup property is set to true. If we disable it or set it to false, then the module takes precedence first. If not found in the module, then it goes to the parent or host application and tries to locate the layout. That's it, so if I place a main.cfm layout in my parent structure, the that one will take precedence.