# The Decorated Variables

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/ModuleConfig.jpg)

At runtime, the configuration object will be created by ColdBox and decorated with the following private properties \(available in the `variables` scope\):

| Property | Description |
| --- | --- |
| appMapping | The `appMapping` setting of the current host application |
| binder | The WireBox configuration binder object |
| cachebox | A reference to CacheBox |
| controller | A reference to the application's ColdBox Controller |
| log | A pre-configured LogBox Logger object for this specific class object \(`coldbox.system.logging.Logger`\) |
| logBox | A Reference to LogBox |
| moduleMapping | The `moduleMapping` setting of the current module. This is the path needed in order to instantiate CFCs in the module. |
| modulePath | The absolute path to the current loading module |
| wirebox | A Reference to WireBox |

You can use any of these private variables to create module settings, load CFCs, add binder mappings, etc.

