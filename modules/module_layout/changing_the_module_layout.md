# Changing The Module Layout

If you are picky and you would like to change the folder layout for your module, you can. This is achieved from within the *ModuleConfig.cfc* by adding a conventions structure that will explain to the ColdBox Module engine how to locate and configure your module. Below is the structure:

```js
// Module Conventions
conventions = {
  handlersLocation = "handlers",
  viewsLocation = "views",
  layoutsLocation = "layouts",
  pluginsLocation = "plugins",
  modelsLocation = "model"
};
```

