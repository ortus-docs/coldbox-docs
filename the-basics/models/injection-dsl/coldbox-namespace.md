# ColdBox Namespace

Whenever your models need anything from the ColdBox application then you can leverage the `coldbox:` namespace for injections.

### 1st Level DSL

| `DSL` | Description |
| :--- | :--- |
| `coldbox` | Get the ColdBox controller reference |

### 2nd Level DSL

| DSL | Description |
| :--- | :--- |
| `coldbox:appScheduler` | Get a reference to the global application scheduler |
| `coldbox:asyncManager` | Get a reference to the ColdBox Async Manager |
| `coldbox:configSettings` | Get the application's configuration structure |
| `coldbox:coldboxSettings` | Get the framework's configuration structure |
| `coldbox:dataMarshaller` | Get the ColdBox data marshaling reference |
| `coldbox:flash` | Get a reference to the application's flash scope object |
| `coldbox:handlerService` | Get a reference to the handler service |
| `coldbox:interceptorService` | Get a reference to the interceptor service |
| `coldbox:loaderService` | Get a reference to the loader service |
| `coldbox:moduleService` | Get a reference to the ColdBox Module Service |
| `coldbox:moduleConfig` | Get a reference to the entire `modules` configuration struct |
| `coldbox:renderer` | Get the ColdBox rendering engine reference |
| `coldbox:requestService` | Get a reference to the request service |
| `coldbox:requestContext` | Get a reference to the current request context object in the request. |
| `coldbox:router` | Get a reference to the application global router.cfc |
| `coldbox:routingService` | Get a reference to the Routing Service |
| `coldbox:schedulerService` | Get a reference to the Scheduler Service |

### 3rd Level DSL

| DSL | Description |
| :--- | :--- |
| `coldbox:interceptor:{name}` |  |
| `coldbox:moduleSettings:{module}` | Inject the entire _{module}_ settings structure |
| `coldbox:moduleConfig:{module}` | Inject the entire _{module}_ configurations structure |
| `coldbox:coldboxSetting:{setting}` | Get a ColdBox setting _{setting}_ and inject it |
| `coldbox:setting:{setting}` | Get the ColdBox application _{setting}_ setting and inject it |
| `coldbox:setting:{setting}@{module}` | Get the ColdBox application _{setting}_ from the _{module}_ and inject it |

### 4th Level DSL

| `DSL` | Description |
| :--- | :--- |
| `coldbox:moduleSettings:{module}:{setting}` | Get a module setting. Very similar to the 3rd level dsl |

```javascript
// some examples
property name="moduleService"   inject="coldbox:moduleService";
property name="producer"        inject="coldbox:interceptor:MessageProducer";
property name="appPath"         inject="coldbox:coldboxSetting:ApplicationPath";
```

