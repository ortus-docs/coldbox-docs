# Reiniting An Application

There will be a time you do changes to your `ColdBox.cfc` configuration file or anything that might be cached.  You will then need to issue a reinit to the framework so the changes can take effect.  You can do so in two ways:

1. CommandBox
2. URL Action


## Reinit via CommandBox

Just simply type

```bash
coldbox reinit
```

You can also pass an optional password to it if you have used the `coldbox.reinitPassword` directive:

```bash
coldbox reinit password=test
```

## URL action

You will use the `fwreinit` URL action which can be anything or if you have setup a password, the password.

```
// no password
index.cfm?fwreinit=1

// password
index.cfm?fwreinit=mypassword
```

