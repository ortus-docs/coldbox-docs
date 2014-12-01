# What's New With 4.0.0

## Introduction

ColdBox 4 is a major release in our ColdBox Platform series and includes
a new revamped MVC core and all extra functionality has been refactored
into modules. We have pushed the modular architecture to 1st class
citizen even in the core itself. There are several [compatibility
updates][] that you must do in order to upgrade your ColdBox 3.X
applications to CodlBox 4 standards. You will notice that the source and
download of ColdBox 4 has been reduced by almost 75% in size. This is
now due to our modular approach were functionality can just be brought
in dynamically. How you might say?

### CommandBox

[CommandBox][], our new ColdFusion (CFML) command line interface,
package manager and REPL. You can now use CommandBox to install
dependencies, modules and even ColdBox itself all from our centralized
code repository: ForgeBox ([1][]). To install ColdBox 4 Bleeding Edge
you can just type:

``` {.javascript}
box install coldbox-be
```

You can even use CommandBox to generate ColdBox applications, modules,
handlers, etc. It has a plethora of commands to get you started in a
fantastic ColdBox Adventure:

``` {.javascript}
// Get help on all the ColdBox commands
coldbox help
```

``` {.javascript}
// create a ColdBox app with TestBox support
coldbox create app MyFirstApp --installTestBox
```

Internal Library Updates
------------------------

Below are all the internal library updates and what's new with each
library release.

-   [ WireBox 2.0.0][]
-   [ CacheBox 2.0.0][]
-   [ LogBox 2.0.0][]

Release Notes
-------------

<h3>
Bug Fixes
</h3>