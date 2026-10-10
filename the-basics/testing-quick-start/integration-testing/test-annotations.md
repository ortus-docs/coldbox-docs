---
description: Test bundle annotations appMapping, configMapping, loadColdBox, and unloadColdBox control the test application.
---

# Test Annotations

Here are the annotations you can add to your testing bundle class to change behavior:

| Annotation      | Type    | Required | Default                           | Description                                                                                                                                                                                                             |
| --------------- | ------- | -------- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `appMapping`    | string  | false    | `/`                               | The application mapping of the ColdBox application to test. By defaults it maps to the root. Extremely important this mapping is a slash notation that points to the root of the ColdBox application to test.           |
| `configMapping` | string  | false    | `{appMapping}/config/Coldbox.cfc` | The configuration file to load for this test, which by convention uses the same configuration as the application uses. This is a dot notation path to a configuration class.                                              |
| `loadColdBox`   | boolean | false    | true                              | By default the base test case will load the virtual application into the `application`scope so all specs can execute                                                                                                    |
| `unloadColdBox` | boolean | false    | true                              | The base test case will unload the virtual application from the `application` scope after all specs have executed.                                                                                                      |

**Examples**

{% tabs %}
{% tab title="BoxLang" %}
```js
@appMapping( "/apps/MyApp" )
class extends="coldbox.system.testing.BaseTestCase" {}

@appMapping( "/apps/MyApp" )
@configMapping( "apps.MyApp.test.resources.Config" )
class extends="coldbox.system.testing.BaseTestCase" {}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component extends="coldbox.system.testing.BaseTestCase" appMapping="/apps/MyApp"{}

component extends="coldbox.system.testing.BaseTestCase"
    appMapping="/apps/MyApp" 
    configMapping="apps.MyApp.test.resources.Config"{}
```
{% endtab %}
{% endtabs %}

> **Caution** The `AppMapping` setting is the most important one. This is how your test connects to a location of a ColdBox application to test.

