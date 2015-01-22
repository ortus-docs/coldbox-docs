# Configuration

In order to create a ColdBox application you must adhere to some naming conventions and a pre-set directory structure. This is needed in order for ColdBox to find what it needs and to help you find modules more easily instead of configuring every single area of your application.  **ColdBox relies on conventions instead of configurations.**

You can also configure many aspects of ColdBox, third-party modules and application settings in our programmtic configuration object: `ColdBox.cfc`.  We will explore this object further in this section.

> **Hint** : ColdBox also allows you to create your own conventions via our configuration object as well.

# Reinitializing An Application
Please note that anytime you make any configuration changes or there are things in memory you wish to clear out, you will be using a URL action that will tell the ColdBox Bootstrapper to reinitialize the application.
