# Loading a custom ColdBox configuration file


### Introduction

By convention the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) used in your application is *config/ColdBox.cfc*. However, you can change the location of this configuration CFC by updating your Application.cfc.

##### Application.cfc

Open the Application.cfc and look for the following:

```js
<---  COLDBOX PROPERTIES --->
<cfset COLDBOX_CONFIG_FILE = "">
```

The value is a full instantiation path to the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) you would like to use:


```js
<---  COLDBOX PROPERTIES --->
<cfset COLDBOX_CONFIG_FILE = "externalMapping.config.ColdBox">
```

### Conclusion

As you can see, it is relatively easy to declare a config file other than the convention standard. This is really useful in multi-tiered environments, unit testing and development environments. Remember that all you need to do is set the COLDBOX_CONFIG_FILE variable before the inclusion of the framework. Enjoy!





