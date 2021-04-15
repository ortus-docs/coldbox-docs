# Interceptor Declaration

Interceptors can be declared in the `Coldbox.cfc` configuration file or programmatically at runtime. If you register them in the configuration file, then you have control over the order in which they fire. If you register interceptors programmatically, you won't have control over the order of execution.

In the configuration file, interceptors are declared as an array of structures in an element called `interceptors`. The elements of each interceptor structure are:

* `class` - The required instantiation path of the CFC.
* `name` - An optional unique name for the interceptor. If this is not passed then the name of the CFC will be used. We highly encourage the use of a name to avoid collisions.
* `properties` - A structure of configuration properties to be passed to the interceptor

In `ColdBox.cfc`:

```text
// Interceptors registration
interceptors = [
    { 
        class = "cfc.path",
        name = "unique name/wirebox ID",
        properties = { 
            // configuration
        }
    }
];
```

**Example**

```text
// Interceptors registration
interceptors = [
    {
        class="coldbox.system.interceptors.SES",
        properties={}
    },
    {
        class="interceptors.RequestTrim",
        properties={
            trimAll=true
        }
    },
    {
        class="coldbox.system.interceptors.Security",
        name="AppSecurity",
        properties={
            rulesSource="model",
            rulesModel="securityRuleService@cb",
            rulesModelMethod="getSecurityRules",
            validatorModel="securityService@cb"
        }
    }
];
```

> **Info** Remember that order is important!

