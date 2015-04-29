# Interceptor Declaration

Interceptors can be declared in the `Coldbox.cfc` or programmatically at runtime. If you declare them in the configuration file, then you have control in the order in which they fire. If you register interceptors programmtically, you won't have control of order of execution. Interceptors are declared as an array of structures in an element called `interceptors`. The elements of the structure are:

* `class` - The instantiation path of the CFC.
* `name` - An optional unique name for the interceptor, if not passed then the name of the CFC will be used. We highly encourage the use of a name to avoid collisions.
* `properties` - A structure of configuration properties for the interceptor


**Template**
```js
interceptors = [
    { 
        class   = "cfc.path",
        name    = "unique.name + wirebox ID",
        properties = { 
            //configuraiton struct
        }
]
```

**Example**

```js
interceptors = [
	{class="coldbox.system.interceptors.SES", properties={}},
	{class="interceptors.RequestTrim", properties={trimAll=true}},
	{class="coldbox.system.interceptors.Security", name="AppSecurity", properties={
		rulesSource 	= "model",
		rulesModel		= "securityRuleService@cb",
		rulesModelMethod = "getSecurityRules",
		validatorModel = "securityService@cb"}
	}}
];
```

> **Info** Remember that order is important!

