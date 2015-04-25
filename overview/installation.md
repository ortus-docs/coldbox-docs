# Installation

In this overview we will investigate on getting started quickly and showcasing how to build ColdBox applications by leveraging our CLI, CommandBox.  To get started open the CommandBox binary or enter the shell by typing `box` in your terminal or console.  Then let's create a new folder and install ColdBox.

```bash
mkdir myapp
box install coldbox
```

This will install the latest stable build in the `myapp` folder.  You can also install the latest bleeding edge version by using the `coldbox-be` slug instead: `install coldbox-be`

## Mapping Installation
You can also install ColdBox outside of your webroot (secured) by just creating a global administrator mapping or a per-application mapping:

```js

this.mappings[ "/coldbox" ] = "/opt/frameworks/coldbox";
```