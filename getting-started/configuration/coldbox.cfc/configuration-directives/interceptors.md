# Interceptors

This is an array of interceptor definitions that you will use to register in your application. The key about this array is that **ORDER** matters. The interceptors will fire in the order that you register them whenever their interception points are announced, so please watch out for this caveat. Each array element is a structure that describes what interceptor to register.

```javascript
//Register interceptors as an array, we need order
interceptors = [

    { 
        // The CFC instantiation path
        class="",
        // The alias to register in WireBox, if not defined it uses the name of the CFC
        name="",
        // A struct of data to configure the interceptor with.
        properties={}
    }

    { class="mypath.MyInterceptor",
      name="MyInterceptor",
      properties={useSetterInjection=false}
    },
    { class="coldbox.system.interceptors.SES", name="MySES" }
];
```

> **Warning** : Important: Order of declaration matters! Also, when declaring multiple instances of the same CFC \(interceptor\), make sure you use the name attribute in order to distinguish them. If not, only one will be registered \(the last one declared\).

