# Configuration

In order to create a ColdBox application you must adhere to some naming conventions and a pre-set directory structure. This is needed in order for ColdBox to find what it needs and to help you find modules more easily instead of configuring every single area of your application. 

> ColdBox relies on conventions instead of configurations.

You can also configure many aspects of ColdBox, third-party modules and application settings in our programmtic configuration object: `ColdBox.cfc`.

## Reinitializing An Application

Please note that anytime you make any configuration changes or there are things in memory you wish to clear out, you will be using a URL action that will tell the ColdBox [Bootstrapper](bootstrapper.md) to reinitialize the application. This special URL variable is called `fwreinit` and can be any value or a specific password you setup in the [ColdBox configuration directive](coldbox.cfc/configuration-directives/coldbox.md).

```text
// reinit with no password
index.cfm?fwreinit=1

// reinit with password
index.cfm?fwreinit=mypass
```

You can also use CommandBox to reinit your application if you are using its embedded server:

```bash
CommandBox>coldbox reinit
```

