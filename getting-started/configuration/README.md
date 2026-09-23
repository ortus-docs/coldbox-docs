---
description: >-
  Learn how to configure ColdBox programmatically via config/ColdBox.bx (or
  .cfc for CFML). Understand conventions over configuration and fine-grained
  control settings.
icon: code-simple
---

# Configuration

In this area we will learn how to configure ColdBox programmatically via the `config/ColdBox.bx` (or `.cfc` for CFML) file. Most of the configurations in ColdBox are pre-set thanks to it's conventions over configuration approach. So the majority of settings are for fine-grained control, third-party modules and more.

{% hint style="info" %}
ColdBox relies on **conventions** instead of configurations.
{% endhint %}

{% hint style="danger" %}
If you make changes to any of the main configuration files you will need to re-initialize your application for the settings to take effect.
{% endhint %}

## Re-initializing an Application

Please note that anytime you make any configuration changes or there are things in memory you wish to clear out, you will be using a URL action that will tell the ColdBox [Bootstrapper](bootstrapper-application.cfc.md) to reinitialize the application. This special URL variable is called `fwreinit` and can be any value or a specific password you setup in the [ColdBox configuration directive](../../reference/configuration-directives/coldbox.md).

```
// reinit with no password
index.bxm?fwreinit=1
index.cfm?fwreinit=1

// reinit with password
index.bxm?fwreinit=mypass
index.cfm?fwreinit=mypass
```

You can also use CommandBox CLI to reinit your application if you are using its embedded server:

```bash
$ coldbox reinit
```
