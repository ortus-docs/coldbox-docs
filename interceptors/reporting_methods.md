# Reporting Methods

There are several reporting and utility methods in the interceptor service that I recommend you explore. Below are some sample methods:

```js
//get the entire state container for preProcess For metadata or reporting
getController().getInterceptorService().getStateContainer('preProcess');

//Get all the interception state containers for metadata or reporting
getController().getInterceptorService().getInterceptionStates();

//Get all the interception points registered in the application
getController().getInterceptorService().getInterceptionPoints();
```

