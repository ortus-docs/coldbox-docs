# Flash

This directive is how you will configure the [Flash RAM](../../flash_ram/flash_ram.md) for operation.

```js
// flash scope configuration
flash = {
	scope = "session,client,cluster,ColdboxCache,or full path",
	properties = {}, // constructor properties for the flash scope implementation
	inflateToRC = true, // automatically inflate flash data into the RC scope
	inflateToPRC = false, // automatically inflate flash data into the PRC scope
	autoPurge = true, // automatically purge flash data for you
	autoSave = true // automatically save flash scopes at end of a request and on relocations.
};
```