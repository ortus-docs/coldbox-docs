---
description: Parent application modules settings in ColdBox.bx — include and exclude lists that control which modules load.
---

# Parent Configuration

There are a few parent application settings when dealing with modules. In your `ColdBox.bx` (or `.cfc` for CFML) you can have a `modules` structure with some configuration settings.

```
modules = {
    // An array or list of the module names that will load ONLY
    include = [],
    // An array or list of the module names that will be EXCLUDED
    exclude = ["paidModule1","paidModule2"]
};
```
