# Overview

Welcome to the world of ColdBox! We are excited you are taking this development journey with us. This manual will give you all the tools and insights so you can master ColdBox.

## CommandBox CLI

![](../../.gitbook/assets/commandboxlogo.png)

The first step in our journey is to [install](http://commandbox.ortusbooks.com/content/setup/installation.html) CommandBox. [CommandBox](http://www.ortussolutions.com/products/commandbox) is a ColdFusion \(CFML\) Command Line Interface \(CLI\), REPL, Package Manager and Embedded Server. We will be using CommandBox for almost every excercise in this manual and it will also allow you to get up and running with ColdFusion and ColdBox in a much speedier manner.

> **Note** : However, you can use your own ColdFusion server setup as you see fit. We use CommandBox as everything is scriptable and fast!

### Download CommandBox

You can download CommandBox from the official site: [http://www.ortussolutions.com/products/commandbox\#download](http://www.ortussolutions.com/products/commandbox#download) and install in your preferred Operating System \(Windows, Mac, \*unix\). CommandBox comes in two flavors:

1. No Java Runtime \(30mb\)
2. Embedded Runtime \(80mb\)

So make sure you choose your desired installation path and follow the instructions here: [http://commandbox.ortusbooks.com/content/setup/installation.html](http://commandbox.ortusbooks.com/content/setup/installation.html)

### Starting CommandBox

Once you download and expand CommandBox you will have the `box.exe` or `box` binary, which you can place in your Windows Path or \*Unix `/usr/bin` folder to have it available system wide. Then just open the binary and CommandBox will unpack itself your user's directory: `{User}/.CommandBox`. This happens only once and the next thing you know, you are in the CommandBox interactive shell!

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/commandbox-terminal.png)

We will be able to execute a-la-carte commands from our command line or go into the interactive shell for multiple commands. We recommend the interactive shell as it is faster and can remain open in your project root.

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

