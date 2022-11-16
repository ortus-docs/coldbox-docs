# Executor Namespace

The executor namespace is both available in ColdBox and WireBox standalone and it is used to get references to created asynchronous executor thread pools.

| DSL               | Description                                           |
| ----------------- | ----------------------------------------------------- |
| `executor`        | Inject an executor using the property name as the key |
| `executor:{name}` | Inject an executor by name                            |



```javascript
property name="coldbox-tasks" inject="executor";

property name="taskExecutor" inject="executor:myTasks";
```
