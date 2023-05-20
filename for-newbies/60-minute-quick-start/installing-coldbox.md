# Installing ColdBox

**Welcome to the world of ColdBox!**&#x20;

We are excited you are taking this development journey with us. Before we get started with ColdBox let's install CommandBox CLI, which will allow you to install/uninstall dependencies, start servers, have a REPL tool and much more. **Please remember to** [**Star**](https://github.com/coldbox/coldbox-platform) **us in Github.**

## IDE Tools

ColdBox has the following supported IDE Tools:

* Sublime - [https://packagecontrol.io/packages/ColdBox Platform](https://packagecontrol.io/packages/ColdBox%20Platform)
* VSCode - [https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-coldbox](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-coldbox)

## CommandBox CLI

<div align="left">

<img src="../../.gitbook/assets/CommandBoxLogo.png" alt="">

</div>

The first step in our journey is to [install](https://commandbox.ortusbooks.com/content/setup/installation.html) CommandBox. [CommandBox](https://www.ortussolutions.com/products/commandbox) is a ColdFusion (CFML) Command Line Interface (CLI), REPL, Package Manager, and Embedded Server. We will be using CommandBox for almost every exercise in this book, and it will also allow you to get up and running with ColdFusion and ColdBox in a much speedier manner.

### Download CommandBox

You can download CommandBox from the official site: [https://www.ortussolutions.com/products/commandbox#download](https://www.ortussolutions.com/products/commandbox#download) and install on your preferred Operating System (Windows, Mac, \*unix). CommandBox comes in two flavors:

1. No Java Runtime (30mb)
2. Embedded Runtime (80mb)

So make sure you choose your desired installation path and follow the instructions here: [https://commandbox.ortusbooks.com/setup/installation](https://commandbox.ortusbooks.com/setup/installation)

### Starting CommandBox

Once you download and expand CommandBox, you will have the `box.exe` or `box` binary, which you can place in your Windows Path or \*Unix `/usr/bin` folder to have it available system-wide. Then open the binary, and CommandBox will unpack to your user's directory: `{User}/.CommandBox`. This happens only once, and the next thing you know, you are in the CommandBox interactive shell!

![CommandBox Shell](<../../.gitbook/assets/image (3) (1) (1).png>)

We can execute a-la-carte commands from our command line or go into the interactive shell for multiple commands. We recommend the interactive shell as it is faster and can remain open in your project root.

{% hint style="info" %}
&#x20;All examples in this book are based on having an interactive shell open.
{% endhint %}

## Installing ColdBox CLI

We can now use CommandBox to install the ColdBox CLI to assist us in all aspects of ColdBox Development:

```bash
install coldbox-cli
```

You will now have a `coldbox` command namespace.  You can do `coldbox help` and get all the help you need with the commands.

## Installing ColdBox

To get started, open the CommandBox binary or enter the shell by typing `box` in your terminal or console. Then let's create a new folder and install ColdBox into a directory.

```bash
mkdir 60-minute-quickstart --cd
install coldbox
```

CommandBox will resolve `coldbox` from ForgeBox ([www.forgebox.io](https://www.forgebox.io)); use the **latest version** available, and download and install it in this folder alongside a `box.json` file that represents your application package.

```
Dir coldbox
File box.json
```

{% hint style="info" %}
**Tip :** You can also install the latest bleeding-edge version by using the `coldbox@be` slug instead, or any previous version.
{% endhint %}

CommandBox can now track this version of ColdBox for you in this directory. In the [next section](my-first-coldbox-application.md) we will scaffold a ColdBox application using an application template.

{% hint style="success" %}
You can find many scaffolding templates for ColdBox in our Github organization: [github.com/coldbox-templates](https://github.com/coldbox-templates)
{% endhint %}

## Uninstalling ColdBox

To uninstall ColdBox from this application folder, just type `uninstall coldbox`.  Try it out!

## Updating ColdBox

To update ColdBox from a previous version, just type `update coldbox`.
