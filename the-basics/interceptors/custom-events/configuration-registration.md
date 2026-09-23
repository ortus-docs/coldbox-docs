---
description: Register custom interception points in the ColdBox configuration via interceptorSettings and the customInterceptionPoints key.
---

# Configuration Registration

In the `ColdBox.bx` (or `.cfc` for CFML) configuration file, there is a structure called `interceptorSettings` with one key:

* `customInterceptionPoints` - This key is a comma delimited list of custom interception points you will be registering for execution.

```javascript
//Interceptor Settings
interceptorSettings = {
    customInterceptionPoints = "onLogin,onWikiTranslation,onAppClose"
};
```

The `customInterceptionPoints` is what interest us. This can be a list or an array of events your system can broadcast. This way, whenever interceptors are registered, they will be inspected for those events.
