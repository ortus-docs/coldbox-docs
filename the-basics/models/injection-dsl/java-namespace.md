---
description: java injection DSL namespace for injecting Java class references into your models.
---

# Java Namespace

You can also request Java objects from the injection dsl.

| DSL            | Description                              |
| -------------- | ---------------------------------------- |
| `java:{class}` | Get a reference to the passed in `class` |

```javascript
property name="duration" inject="java:java.time.Duration";
```
