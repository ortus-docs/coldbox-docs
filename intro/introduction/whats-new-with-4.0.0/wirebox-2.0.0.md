# WireBox 2.0.0

## Introduction

WireBox 2.0.0 is a major release of our Dependency Injection and AOP library with some major fixes and some cool new updates.

## Release Notes

You can find the release version information here: [https://ortussolutions.atlassian.net/browse/WIREBOX/fixforversion/12300](https://ortussolutions.atlassian.net/browse/WIREBOX/fixforversion/12300)

Bugs

* \[[WIREBOX-31](https://ortussolutions.atlassian.net/browse/WIREBOX-31)\] -Builder.buildDSLDependency does not use the custom DSL correctly as the default namespace kicks in

Improvements

* \[[WIREBOX-32](https://ortussolutions.atlassian.net/browse/WIREBOX-32)\] -binder.mapDirectory now skips hidden dirs
* \[[WIREBOX-35](https://ortussolutions.atlassian.net/browse/WIREBOX-35)\] -Remove coldbox:cacheManager DSL reference, it is no longer valid

New Features

* \[[WIREBOX-2](https://ortussolutions.atlassian.net/browse/WIREBOX-2)\] -allow mapDSL to be altered at runtime by modules via new wirebox method: registerDSL\(\)
* \[[WIREBOX-30](https://ortussolutions.atlassian.net/browse/WIREBOX-30)\] -Support wiring new injection DSL = byType, which leverages the type to match to an implementation
* \[[WIREBOX-33](https://ortussolutions.atlassian.net/browse/WIREBOX-33)\] -Add a force argument to the map functions so you can override if a mapping already exists
* \[[WIREBOX-36](https://ortussolutions.atlassian.net/browse/WIREBOX-36)\] -New coldbox dsl to get a renderer reference: coldbox:renderer

## Major Enhancements

* All [LogBox](logbox-2.0.0.md) libraries updated
* All [CacheBox](cachebox-2.0.0.md) libraries updated

### ColdBox DSL Updates

The ColdBox injection DSL has had major updates to get to the ColdBox 4 standards.

* All **ocm** injections have been removed in preference to

  **cachebox** injection DSL

* The **coldbox:cachemanager** DSL has been removed in preference to

  **cachebox** injection DSL

* All **plugin** injections have been deprecated in preference to

  model/object injections

* New **coldbox:renderer** dsl to inject the new ColdBox system

  renderer

### Forced Mappings

The _map\(\)_ function on the Configuration Binder now has a **force** argument which allows you to map no matter if the mapping exists or not already.

```javascript
map( alias="MyService", force=true )
    .to( "model.MyService" );
```

### Dynamic Custom DSL Registration

Injectors allow you to register custom DSLs at runtime by using the **registerDSL\(\)** method on any injector. This feature was mostly done for modules, so they could enhance WireBox in a ColdBox context. However, this also allows you to leverage this in any non-ColdBox applications.

```javascript
// Register Custom DSL
controller.getWireBox()
    .registerDSL( namespace="javaloader", path="#moduleMapping#.model.JavaLoaderDSL" );
```

### Injection By Type: byType DSL

We have expanded our custom DSL and injectors to allow you to do injection by ColdFusion types. This feature is more in-line with features from Java or static languages were you can tell injectors to inject by argument or property type. Let's say you have a package of interfaces with subpackages of implementations:

```text
/app/pkg/ISomeObject.cfc // <- interface
/app/pkg/adb/SomeObject.cfc // <- component implementing the above interface.
/app/pkg/odb/SomeObject.cfc // <- component implementing the above interface.
```

You then want to rely on the interface **type** field of properties in my dependent CFCs and leveraging the **byType** injection DSL. You would first map the right implementation using the alias as the name of the Interface.

```javascript
// mappings...
map( "app.pkg.ISomeObject" ).to( "app.pkg.adb.SomeObject" );

// Injection
component {

    /**
     * @inject byType
     */
    property app.pkg.ISomeObject object;

}
```

