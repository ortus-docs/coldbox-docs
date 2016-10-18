# Flash

This directive is how you will configure the [Flash RAM](../../../flash_ram/index.md) for operation.  Below are the configuration keys and their defaults:

```js
// flash scope configuration
flash = {
    // Available scopes are: session,client,cache,mock or your own class path
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

