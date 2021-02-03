# Configuration

In this area we will learn how to configure ColdBox programmatically via the `config/ColdBox.cfc` file.  Most of the configurations in ColdBox are pre-set thanks to it's conventions over configuration approach.  So the majority of settings are for fine-grained control, third-party modules and more.

{% hint style="info" %}
ColdBox relies on **conventions** instead of configurations.
{% endhint %}

If you make changes to any of the main configuration files you will need to re-initialize your application for the settings to take effect.

## Re-initializing an Application

Please note that anytime you make any configuration changes or there are things in memory you wish to clear out, you will be using a URL action that will tell the ColdBox [Bootstrapper](bootstrapper-application.cfc.md) to reinitialize the application. This special URL variable is called `fwreinit` and can be any value or a specific password you setup in the [ColdBox configuration directive](coldbox.cfc/configuration-directives/).

```text
// reinit with no password
index.cfm?fwreinit=1

// reinit with password
index.cfm?fwreinit=mypass
```

You can also use CommandBox CLI to reinit your application if you are using its embedded server:

```bash
$ coldbox reinit
```

