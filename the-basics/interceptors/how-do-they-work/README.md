# How do they work?

Interceptors are CFCs that extend the ColdBox Interceptor class \(`coldbox.system.Interceptor`\), implement a configuration method called `configure()`, and then contain methods for the events it will listen for. All interceptors are treated as **singletons** in your application, so make sure they are thread safe and var scoped.

![](../../../.gitbook/assets/coldboxmajorclasses.jpg)

```text
/**
* My Interceptor
*/
component extends="coldbox.system.Interceptor"{

    function configure(){}

    function preProcess( event, interceptData, buffer, rc, prc ){}
}
```

> **Info** You can also remove the inheritance from the CFC \(preferred method\) and WireBox will extend the `coldbox.system.Interceptor` for you using [Virtual Inheritance](https://wirebox.ortusbooks.com/content/virtual_inheritance/).

You can use CommandBox to create interceptors as well:

```bash
coldbox create interceptor help
```

## Registration

Interceptors can be registered in your `Coldbox.cfc` configuration file using the `interceptors` struct, or they can be registered manually via the system's interceptor service.

In `ColdBox.cfc`:

```text
// Interceptors registration
interceptors = [
    {
        class   = "cfc.path", //by default this should be interceptors.yourcfcname  
        name    = "unique name/wirebox ID",
        properties = {
            // configuration
        }
    }
];
```

Registered manually:

```text
controller.getInterceptorService().registerInterceptor( interceptorClass="cfc.path" );
```

## The `configure()` Method

The interceptor has one important method that you can use for configuration called `configure()`. This method will be called right after the interceptor gets created and injected with your interceptor properties.

> **Info** These properties are local to the interceptor only!

As you can see on the diagram, the interceptor class is part of the ColdBox framework super type family, and thus inheriting the functionality of the framework.

