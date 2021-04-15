# Provider Namespace

Injects providers of objects, meaning a proxy object that knows how to retrieve and construct the desired target object.  This is a great way to delay construction or deal with non-singleton objects without causing memory leaks.

| DSL | Description |
| :--- | :--- |
| `provider` | Build an object provider that will return the mapping according to the property, method or argument name. |
| `provider:{name}` | Build an object provider that will return the `{name}` mapping. |
| `provider:{injectionDSL}` | Build an object provider that will return the object that the {`injectionDSL`} refers to |

```javascript
// using id
property name="timedService" inject="provider:TimedService";

// using DSL
property name="timedService" inject="provider:logbox:logger:{this}";
```

