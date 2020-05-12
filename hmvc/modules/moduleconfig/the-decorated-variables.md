# The Decorated Variables

![](../../../.gitbook/assets/moduleconfig.jpg)

At runtime, the configuration object will be created by ColdBox and decorated with the following private properties \(available in the `variables` scope\):

| Property | Description |
| :--- | :--- |
| `appMapping` | The `appMapping` setting of the current host application |
| `appRouter` | The main application's URL router object |
| `binder` | The WireBox configuration binder object |
| `cachebox` | A reference to CacheBox |
| `controller` | A reference to the application's ColdBox Controller |
| `coldboxVersion` | The current ColdBox Version |
| `log` | A pre-configured LogBox Logger object for this specific class object \(`coldbox.system.logging.Logger`\) |
| `logBox` | A Reference to LogBox |
| `moduleMapping` | The `moduleMapping` setting of the current module. This is the path needed in order to instantiate CFCs in the module. |
| `modulePath` | The absolute path to the current loading module |
| `router` | The module's URL router object |
| `wirebox` | A Reference to WireBox |

You can use any of these private variables to create module settings, load CFCs, add binder mappings, etc.

