---
description: Change default module folder conventions via the conventions structure in ModuleConfig.
---

# Changing The Module Layout

If you are picky and you would like to change the folder layout for your module, you can. This is achieved from within the `ModuleConfig.bx` (or `.cfc` for CFML) by adding a `conventions` structure that will explain to the ColdBox Module engine how to locate and configure your module.

```javascript
// Module Conventions
conventions = {
  handlersLocation  = "handlers",
  viewsLocation     = "views",
  layoutsLocation   = "layouts",
  modelsLocation    = "models"
};
```
