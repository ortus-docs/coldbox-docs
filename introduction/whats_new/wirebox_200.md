# WireBox 2.0.0

## Introduction

WireBox  2.0.0 is a major release of our Dependency Injection and AOP library with some major fixes and some cool new updates.

## Release Notes

                
<h3>Bugs
</h3>
<ul>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-31'>WIREBOX-31</a>] -Builder.buildDSLDependency does not use the custom DSL correctly as the default namespace kicks in
</li>
</ul>
                        
<h3>Improvements
</h3>
<ul>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-32'>WIREBOX-32</a>] -binder.mapDirectory now skips hidden dirs
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-35'>WIREBOX-35</a>] -Remove coldbox:cacheManager DSL reference, it is no longer valid
</li>
</ul>
        
<h3>New Features</h3>
<ul>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-2'>WIREBOX-2</a>] -allow mapDSL to be altered at runtime by modules via new wirebox method: registerDSL()
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-30'>WIREBOX-30</a>] -Support wiring new injection DSL = byType, which leverages the type to match to an implementation
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-33'>WIREBOX-33</a>] -Add a force argument to the map functions so you can override if a mapping already exists
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/WIREBOX-36'>WIREBOX-36</a>] -New coldbox dsl to get a renderer reference: coldbox:renderer
</li>
</ul>

## Major Enhancements

-   All LogBox libraries updated
-   All CacheBox libraries updated

### ColdBox DSL Updates

The ColdBox injection DSL has had major updates to get to the ColdBox 4
standards.


-   All **ocm** injections have been removed in preference to
    **cachebox** injection DSL
-   The **coldbox:cachemanager** DSL has been removed in preference to
    **cachebox** injection DSL
-   All **plugin** injections have been deprecated in preference to
    model/object injections
-   New **coldbox:renderer** dsl to inject the new ColdBox system
    renderer

### Forced Mappings

The *map()* function on the Configuration Binder now has a **force**
argument which allows you to map no matter if the mapping exists or not
already.

```js
map( alias="MyService", force=true )
    .to( "model.MyService" );
```

### Dynamic Custom DSL Registration

Injectors allow you to register custom DSLs at runtime by using the
**registerDSL()** method on any injector. This feature was mostly done
for modules, so they could enhance WireBox in a ColdBox context.
However, this also allows you to leverage this in any non-ColdBox
applications.

```js
// Register Custom DSL
controller.getWireBox()
    .registerDSL( namespace="javaloader", path="#moduleMapping#.model.JavaLoaderDSL" );
```

### Injection By Type: byType DSL

We have expanded our custom DSL and injectors to allow you to do
injection by ColdFusion types. This feature is more in-line with
features from Java or static languages were you can tell injectors to
inject by argument or property type. Let's say you have a package of
interfaces with subpackages of implementations:

```
/app/pkg/ISomeObject.cfc // <- interface
/app/pkg/adb/SomeObject.cfc // <- component implementing the above interface.
/app/pkg/odb/SomeObject.cfc // <- component implementing the above interface.
```

You then want to rely on the interface **type** field of properties in
my dependent CFCs and leveraging the **byType** injection DSL. You would
first map the right implementation using the alias as the name of the
Interface.

```js
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


