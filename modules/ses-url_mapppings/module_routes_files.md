# Module Routes Files

A module can also include one or more custom routing files in order to take advantage of our routing DSL and also have better separation as they will be stored outside of the module configuration object. You do this by giving the path to the custom file to include in your routes structure:

```js
routes = [
  "config/routes"
];
```

This will look for a routes.cfm template in your module config folder and load it. Please note that you do not need to specify a .cfm if you don't want to. You can load as many route files as you like.