# Installation

In this overview we will investigate getting started quickly and showcase how to build ColdBox applications by leveraging our CLI, [CommandBox](http://www.ortussolutions.com/products/commandbox). To get started, open the CommandBox binary or enter the shell by typing `box` in your terminal or console. Next, let's create a new folder and install ColdBox into our first application.

```bash
mkdir myapp
cd myapp
install coldbox
```

CommandBox will resolve `coldbox`, download and install it in this folder alongside a `box.json` file which represents your application package.

```text
Dir           0 Apr 25,2015 11:04:05 coldbox
File        112 Apr 25,2015 11:04:05 box.json
```

> **Info** You can also install the latest bleeding edge version by using the `coldbox-be` slug instead: `install coldbox-be`

## Mapping Installation

You can also install ColdBox outside of your webroot \(secured\) by just creating a global administrator mapping or a per-application mapping by adding the following to your `Application.cfc`:

```javascript
this.mappings[ "/coldbox" ] = "/opt/frameworks/coldbox";
```

