# LogBox Namespace

This DSL namespace interacts with the loaded LogBox instance.

| DSL | Description |
| :--- | :--- |
| `logbox` | Get a reference to the application's LogBox instance |
| `logbox:root` | Get a reference to the root logger |
| `logbox:logger:{category name}` | Get a reference to a named logger by its category name |
| `logbox:logger:{this}` | Get a reference to a named logger using the current target object's path as the category name |

```javascript
property name="logbox" inject="logbox";
property name="log" inject="logbox:root";
property name="log" inject="logbox:logger:myapi";
property name="log" inject="logbox:logger:{this}";
```

