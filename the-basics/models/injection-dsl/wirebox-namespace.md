# WireBox Namespace

Talk and get objects from the current WireBox injector.

### 1st Level DSL

| DSL | Description |
| :--- | :--- |
| `wirebox` | Get a reference to the current injector |

### 2nd Level DSL

| DSL | Description |
| :--- | :--- |
| `wirebox:asyncManager`  | Get a reference to the Async Manager |
| `wirebox:binder` | Get a reference to the injector's binder |
| `wirebox:eventManager` | Get a reference to injector's event manager |
| `wirebox:parent` | Get a reference to the parent injector \(if any\) |
| `wirebox:properties` | Get the entire properties structure the injector is initialized with. If running within a ColdBox context then it is the structure of application settings |
| `wirebox:populator` | Get a reference to a WireBox's Object Populator utility |

### 3rd Level DSL

| DSL | Description |
| :--- | :--- |
| `wirebox:property:{name}` | Retrieve one key of the properties structure |
| `wirebox:scope:{scope}` | Get a direct reference to an internal or custom scope object |

```javascript
property name="beanFactory" inject="wirebox";
property name="settings" inject="wirebox:properties";
property name="singletonCache" inject="wirebox:scope:singleton";
property name="populator" inject="wirebox:populator";
property name="binder" inject="wirebox:binder";
```

