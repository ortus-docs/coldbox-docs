# The Modules Configuration Object

The module configuration object: *ModuleConfig.cfc* is the boot code of your module and where you can tell the host application how this module will behave. This component is a simple CFC, no inheritance needed, because it is decorated with methods and variables by ColdBox at runtime. The only requirement is that this object MUST exist in the root of the module folder and contain a *configure()* method. This is the way modules are discovered, so if there is no *ModuleConfig.cfc* in the root of that folder inside of the modules folder, the framework skips it. So let's investigate what we need or can do in this component.

