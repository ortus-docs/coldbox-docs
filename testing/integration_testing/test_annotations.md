# Test Annotations

Here are the annotations you can add to your testing bundle CFC.

|Annotation|Type|Required|Default|Description|
|--|--|--|--|--|
|appMapping|string|false|/|The application mapping of the ColdBox application to test. By defaults it maps to the root. Extermely important this mapping is a slash notation that points to the root of the ColdBox application to test.|
|configMapping|string|false|{appMapping}/config/Coldbox.cfc |The configuration file to load for this test, which by convention uses the same configuration as the application uses. This is a dot notation path to a configuration CFC.|
|coldboxAppKey|string|false|*cbController*|The named key of the ColdBox controller that will be placed in application scope for you to simulate the ColdBox application. Used mostly on advanced testing cases where you have altered the default application key.|
|loadColdBox|bollean|false|true|If you call super.init() on the test case, this flag tells the base test case to load up the virtual testing application or not. This flag is mostly used for advanced testing cases, by default it always load ColdBox in Base Test Cases.|

```js
component extends="coldbox.system.testing.BaseTestCase" appMapping="/apps/MyApp"{

component extends="coldbox.system.testing.BaseTestCase"
    appMapping="/apps/MyApp" 
    configMapping="apps.MyApp.test.resources.Config"{
```

> **Caution** The AppMapping setting is the most important one. This is how your test connects to a location of a ColdBox application to test. 

