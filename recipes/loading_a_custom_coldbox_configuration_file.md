# Loading a custom ColdBox configuration file


## Introduction

By convention the `config/ColdBox.cfc` CFC is used as your configuration file. However, you can change the location of this configuration CFC by updating your `Application.cfc`.

### Application.cfc

Open the Application.cfc and look for the following:

```js
<---  COLDBOX PROPERTIES --->
<cfset COLDBOX_CONFIG_FILE = "">
```

The value is a full instantiation path to the `ColdBox.cfc` you would like to use:


```js
<---  COLDBOX PROPERTIES --->
<cfset COLDBOX_CONFIG_FILE = "externalMapping.config.ColdBox">
```



