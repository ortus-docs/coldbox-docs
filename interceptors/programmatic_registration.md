# Programmatic Registration

You can use the interceptor service to register new events or interception points via the appendInterceptionPoints() method:

```js
public any appendInterceptionPoints(any customPoints)
```

The value of the customPoints argument can be a list or an array of interception points to register so the interceptor service can manage them for you:

```js
controller.getInterceptorService()
	.appendInterceptionPoints( ["onLog", "onError", "onRecordInserted"] );
```

