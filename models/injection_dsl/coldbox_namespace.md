# ColdBox Namespace

This namespace is a combination of namespaces that are only active when used within a ColdBox application:

|DSL|Description|
|--|--|
|coldbox |Get the coldbox controller reference|
|coldbox:flash |Get a reference to the application's flash scope object|
|coldbox:setting:{setting} |Get the coldbox application *{setting}* setting and inject it |
|coldbox:fwSetting:{setting} |Get a ColdBox setting *{setting}* and inject it |
|coldbox:setting:{setting}@{module} |Get the coldbox application *{setting}* from the *{module}* and inject it |
|coldbox:datasource:{alias} |Get a new datasource struct according to *{alias}*|
|coldbox:loaderService |Get a reference to the loader service |
|coldbox:requestService |Get a reference to the request service |
|coldbox:handlerService |Get a reference to the handler service |
|coldbox:interceptorService |Get a reference to the interceptor service |
|coldbox:moduleService |Get a reference to the ColdBox Module Service|
|coldbox:interceptor:{name} |Get a reference of a named interceptor *{name}*|
|coldbox:configSettings |Get the application's configuration structure |
|coldbox:fwSettings |Get the framework's configuration structure |
|coldbox:fwSetting:{setting} |Get a setting from the ColdBox settings instead of the Application settings|
|coldbox:moduleSettings:{module} |Inject the entire *{module}* settings structure|
|coldbox:moduleConfig:{module} |Inject the entire *{module}* configurations structure|


```javascript
// some examples
property name="moduleService" inject="coldbox:moduleService";
property name="producer" inject="coldbox:interceptor:MessageProducer";
property name="appPath" inject="coldbox:fwSetting:ApplicationPath";

```
