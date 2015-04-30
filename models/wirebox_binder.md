# WireBox Binder

You can have an optional WireBox configuration binder that can fine-tune the WireBox engine and also where you can create object mappings, and even more model locations by convention. Usually you will find this binder by convention in your `config/WireBox.cfc` location and it looks like this:

```js
component extends="coldbox.system.ioc.config.Binder"{
	
	function configure(){
		
		// Configure WireBox
		wireBox = {
			// Scope registration, automatically register a wirebox injector instance on any CF scope
			// By default it registeres itself on application scope
			scopeRegistration = {
				enabled = true,
				scope   = "application", // server, cluster, session, application
				key		= "wireBox"
			},

			// Custom DSL Namespace registrations
			customDSL = {
				// namespace = "mapping name"
			},
			
			// Custom Storage Scopes
			customScopes = {
				// annotationName = "mapping name"
			},
			
			// Package scan locations or model external locations by convention
			scanLocations = [],
			
			// Stop Recursions
			stopRecursions = [],
			
			// Parent Injector to assign to the configured injector, this must be an object reference
			parentInjector = "",
			
			// Register all event listeners here, they are created in the specified order
			listeners = [
				// { class="", name="", properties={} }
			]			
		};
		
		// Map Bindings below
	}	
}
```

Please refer to the full Binder documentation: (http://wirebox.ortusbooks.com/content/configuring_wirebox/index.html) for further inspection.

