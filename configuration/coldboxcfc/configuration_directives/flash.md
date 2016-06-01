# Flash

This directive is how you will configure the [Flash RAM](../../flash_ram/index.md) for operation.  Below are the configuration keys and their defaults:

```js
// flash scope configuration
flash = {
    // The implementation to use
	scope = "session",
	// constructor properties for the flash scope implementation
	properties = {},
	// automatically inflate flash data into the RC scope at the beginning of a request
	inflateToRC = true, 
	// automatically inflate flash data into the PRC scope at the beginning of a request
	inflateToPRC = false, 
	// automatically purge flash data for you
	autoPurge = true, 
	// automatically save flash scopes at end of a request and on relocations.
	autoSave = true 
};
```

**scope**

The ColdFusion scope to store the Flash data on or the instantiation path of your custom Flash object.  Available core scopes are: session, client, cache, and mock.

