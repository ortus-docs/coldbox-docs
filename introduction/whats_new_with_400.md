# What's New With 4.0.0

## Introduction

ColdBox 4 is a major release in our ColdBox Platform series and includes
a new revamped MVC core and all extra functionality has been refactored
into modules. We have pushed the modular architecture to 1st class
citizen even in the core itself. There are several [compatibility
updates](upgrading_to_coldbox_400.md) that you must do in order to upgrade your ColdBox 3.X
applications to CodlBox 4 standards. You will notice that the source and
download of ColdBox 4 has been reduced by almost 75% in size. This is
now due to our modular approach were functionality can just be brought
in dynamically. How you might say?

### CommandBox

[CommandBox](http://www.ortussolutions.com/products/commandbox), our new ColdFusion (CFML) command line interface,
package manager and REPL. You can now use CommandBox to install
dependencies, modules and even ColdBox itself all from our centralized
code repository: [ForgeBox](http://www.coldbox.org/forgebox). To install ColdBox 4 Bleeding Edge
you can just type:

```bash
box install coldbox-be
```

You can even use CommandBox to generate ColdBox applications, modules,
handlers, etc. It has a plethora of commands to get you started in a
fantastic ColdBox Adventure:

```bash
// Get help on all the ColdBox commands
coldbox help
```

```bash
// create a ColdBox app with TestBox support
coldbox create app MyFirstApp --installTestBox
```

## Internal Library Updates
ColdBox is composed of three internal libraries: WireBox (DI & AOP), CacheBox (Caching), LogBox (Logging). Below you can find what's new with this release for each library:

- [WireBox 2.0.0](whats_new/wirebox_200.md)
- [CacheBox 2.0.0](whats_new/cachebox_200.md)
- [LogBox 2.0.0](whats_new/logbox_200.md)

</h3>