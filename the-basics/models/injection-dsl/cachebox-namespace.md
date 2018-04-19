# CacheBox Namespace

This DSL namespace is only active if using CacheBox or a ColdBox application context.

| DSL | Description |
| --- | --- |
| cachebox | Get a reference to the application's CacheBox instance |
| cachebox:{name} | Get a reference to a named cache inside of CacheBox |
| cachebox:{name}:{objectKey} | Get an object from the named cache inside of CacheBox according to the objectKey |

```javascript
property name="cacheFactory" inject="cacheBox";
property name="cache" inject="cachebox:default";
property name="data" inject="cachebox:default:myKey";
```

