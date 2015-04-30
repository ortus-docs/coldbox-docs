# ColdBox Namespace

This namespace is a combination of namespaces that are only active when used within a ColdBox application:

|DSL|Description|
|--|--|
|coldbox |Get the coldbox controller reference|
|coldbox:flash |Get a reference to the application's flash scope object|
|coldbox:setting:{setting} |Get the coldbox application *{setting}* setting and inject it |
|coldbox:setting:{setting}@{module} |Get the coldbox application *{setting}* from the *{module}* and inject it |
|coldbox:plugin:{plugin} |Get the {plugin} plugin and inject it |
|coldbox:myPlugin:*{MyPlugin}* |Get the *{MyPlugin}* custom plugin and inject it |
|coldbox:myPlugin:{MyPlugin}@{module} |Get the *{MyPlugin}* custom plugin from the *{module}* module and inject it |
|coldbox:datasource:{alias} |Get a new datasource bean according to *{alias}*|
|coldbox:configBean |Get a new config bean object and inject it |
|coldbox:mailsettingsbean |Get a new mail settings bean and inject it |
|coldbox:loaderService |Get a reference to the loader service |
|coldbox:requestService |Get a reference to the request service |
|coldbox:debuggerService |Get a reference to the debugger service |
|coldbox:pluginService |Get a reference to the plugin service |
|coldbox:handlerService |Get a reference to the handler service |
|coldbox:interceptorService |Get a reference to the interceptor service |
|coldbox:moduleService |Get a reference to the ColdBox Module Service|
|coldbox:interceptor:{name} |Get a reference of a named interceptor *{name}*|
|coldbox:cacheManager |get the cache manager |
|coldbox:fwConfigBean |Get a configuration bean object with ColdBox settings instead of Application settings|
|coldbox:fwSetting:{setting} |Get a setting from the ColdBox settings instead of the Application settings|
|coldbox:moduleSettings:{module} |Inject the entire *{module}* settings structure|
|coldbox:moduleConfig:{module} |Inject the entire *{module}* configurations structure|
|ioc|Get the named ioc bean and inject it. Name comes from the cfproperty, setter or argument name|
|ioc:{beanName} |Get the ioc bean according to *{beanName}*|
|javaLoader:{class} |Create an object from the [JavaLoader](http://wiki.coldbox.org/wiki/Plugins:JavaLoader.cfm) plugin and its set of loaded java libraries|
|webservice:{alias} |Get a webservice object using an {alias} that matches in your coldbox configuration file.|


```javascript
// some examples
property name="logbox" inject="logbox";
property name="rootLogger" inject="logbox:root";
property name="logger" inject="logbox:logger:model.com.UserService";
property name="moduleService" inject="coldbox:moduleService";
property name="producer" inject="coldbox:interceptor:MessageProducer";
property name="configBean" inject="coldbox:fwConfigBean";
property name="producer" inject="interceptor:MessageProducer";
property name="appPath" inject="coldbox:fwSetting:ApplicationPath";

// JavaLoader goodness
property name="binaryHeap" inject="javaLoader:org.apache.commons.collections.BinaryHeap";
property name="email" inject="javaLoader:org.apache.commons.mail.SimpleEmail";
```
