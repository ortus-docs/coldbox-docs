---
description: Get up and running with ColdBox easily.
---

# Installation

**Welcome to the world of ColdBox!**

We are excited you are taking this development journey with us. Before we get started with ColdBox let's install CommandBox CLI, which will allow you to install/uninstall dependencies, start servers, have a REPL tool and much more.

## Requirements

Please note that the supported CFML engines can change from major version to major version.  Always verify them in the [project's readme.](https://github.com/coldbox/coldbox-platform)

* Lucee 5+
* Adobe 2018+

## IDE Tools

ColdBox has the following supported IDE Tools:

* Sublime - [https://packagecontrol.io/packages/ColdBox Platform](https://packagecontrol.io/packages/ColdBox%20Platform)
* VSCode - [https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-coldbox](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-coldbox)

## CommandBox CLI

![](../.gitbook/assets/CommandBoxLogo.png)

The first step in our journey is to [install](https://commandbox.ortusbooks.com/content/setup/installation.html) CommandBox. [CommandBox](https://www.ortussolutions.com/products/commandbox) is a ColdFusion (CFML) Command Line Interface (CLI), REPL, Package Manager, and Embedded Server. We will be using CommandBox for almost every exercise in this book, and it will also allow you to get up and running with ColdFusion and ColdBox in a much speedier manner.

> **Note** : However, you can use your own ColdFusion server setup as you see fit. We use CommandBox as everything is scriptable and fast!

### Download CommandBox

You can download CommandBox from the official site: [https://www.ortussolutions.com/products/commandbox#download](https://www.ortussolutions.com/products/commandbox#download) and install in your preferred Operating System (Windows, Mac, \*unix). CommandBox comes in two flavors:

1. No Java Runtime (80mb)
2. Embedded Runtime (120mb)

So make sure you choose your desired installation path and follow the instructions here: [https://commandbox.ortusbooks.com/setup/installation](https://commandbox.ortusbooks.com/setup/installation)

### Starting CommandBox

Once you download and expand CommandBox, you will have the `box.exe` or `box` binary, which you can place in your Windows Path or \*Unix `/usr/bin` folder to have it available system-wide. Then just open the binary and CommandBox will unpack itself your user's directory: `{User}/.CommandBox`. This happens only once and the next thing you know, you are in the CommandBox interactive shell!

![CommandBox Shell](<../.gitbook/assets/image (3) (1) (1).png>)

We can execute a-la-carte commands from our command line or go into the interactive shell for multiple commands. We recommend the interactive shell as it is faster and can remain open in your project root.

{% hint style="info" %}
All examples in this book are based on having an interactive shell open.
{% endhint %}

## Installing ColdBox CLI

The ColdBox CLI is your best friend when developing with ColdBox and it's based on CommadBox.  Just fire up that terminal and install it

```bash
install coldbox-cli
```

Now you will have a `coldbox` namespace of commands.  Explore them `coldbox help.` To install ColdBox, you can do so via `install coldbox` or by scaffolding a starter application template.  We would highly encourage you to visit our [application templates](../digging-deeper/recipes/application-templates.md) section to discover how to get started quickly.

**Please remember to** [**Star**](https://github.com/coldbox/coldbox-platform) **us in Github.**

## Updating ColdBox

To update ColdBox from a previous version, just type `update coldbox`.  You can also run the \
&#x20;command `outdated` periodically to verify the packages in your application.
