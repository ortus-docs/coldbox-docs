# Object Scopes

You can very easily add persistence to your model+ objects via our annotations or binder configuration. The available scopes are:

* `transient or no scope` : The default scope. Meaning objects have no scope, they are recreated every single time you request them.
* `singleton` : Objects are created once and live until your Application expires
* `cachebox` : You can store your objects in any CacheBox provider and even provide timeouts for them
* `session` : Store them in the ColdFusion `session` scope
* `server` : Store them in the ColdFusion `server` scope
* `request` : Store them in the ColdFusion `request` scope
* `application` : Store them in the ColdFusion `application` scope
* `CUSTOM` : You can build your own scopes as well.

```javascript
// transient
component name="MyService"{}

// singleton
component name="MyService" singleton{}

// cache in default provider
component name="MyService" cache="true" cacheTimeout="45" cacheLastAccessTimeout="15"{}

// cache in another provider
component name="MyService" cachebox="MyProvider" cacheTimeout="45"{}

// request scope
component name="MyService" scope="request"{}

// session
component name="MyService" scope="session"{}
```

> **Hint** Pease note that using annotations is optional, you can configure every object in our configuration binder as well.

* Persistence DSL: [http://wirebox.ortusbooks.com/configuration/mapping-dsl/persistence-dsl](http://wirebox.ortusbooks.com/configuration/mapping-dsl/persistence-dsl)
* Object Persistence & Thread Safety: [http://wirebox.ortusbooks.com/advanced-topics/object-persistence-and-thread-safety](http://wirebox.ortusbooks.com/advanced-topics/object-persistence-and-thread-safety)

