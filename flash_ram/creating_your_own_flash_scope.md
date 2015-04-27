# Creating Your Own Flash Scope

The ColdBox Flash capabilities are very flexible and you can easily create your own Flash Implementations by doing two things:

1. Create a CFC that inherits from `coldbox.system.web.flash.AbstractFlashScope`
2. Implement the following functions: `clearFlash(), saveFlash(), flashExists(), and getFlash()`

## Implementable Methods

|Method|ReturnType|Description|
|--|--|--|
|clearFlash()|void|Will destroy or clear the entire flash storage structure.|
|saveFlash()|void|Will be called before relocations or on demand in order to flash the storage. This method usually talks to the getScope() method to retrieve the temporary flash variables and then serialize and persist.|
|flashExists()|boolean|Checks if the flash storage is available and has data in it.|
|getFlash()|struct|This method needs to return a structure of flash data to reinflate and use during a request.|

> **Caution**  It is the developer's responsibility to provide consistent storage locking and synchronizations.

All of the methods must be implemented and they have their unique purposes as you read in the description. Let's see a real life example, below you can see the flash implementation for the session scope:

```js
<cfcomponent output="false" extends="coldbox.system.web.flash.AbstractFlashScope" hint="A ColdBox session flash scope">

<-------------------------------------------- CONSTRUCTOR ------------------------------------------>

	<cfscript>
		instance = structnew();
	</cfscript>

	<---  init --->
    <cffunction name="init" output="false" access="public" returntype="SessionFlash" hint="Constructor">
    	<cfargument name="controller" type="coldbox.system.web.Controller" required="true" hint="The ColdBox Controller"/>
    	<cfscript>
    		super.init(arguments.controller);

			instance.flashKey = "cbox_flash_scope";

			return this;
    	</cfscript>
    </cffunction>

<-------------------------------------------- IMPLEMENTED METHODS ------------------------------------------>

	<---  getFlashKey --->
	<cffunction name="getFlashKey" output="false" access="public" returntype="string" hint="Get the flash key storage used in session scope.">
		<cfreturn instance.flashKey>
	</cffunction>

	<---  clearFlash --->
	<cffunction name="clearFlash" output="false" access="public" returntype="void" hint="Clear the flash storage">
		<cfif flashExists()>
			<cfset structClear(session[getFlashKey()])>
		</cfif>
	</cffunction>

	<---  saveFlash --->
	<cffunction name="saveFlash" output="false" access="public" returntype="void" hint="Save the flash storage in preparing to go to the next request">
		<---  Init The Storage if not Created --->
		<cfif NOT flashExists()>
    		<cflock scope="session" throwontimeout="true" timeout="20">
				<cfif NOT flashExists()>
					<cfset session[getFlashKey()] = structNew()>
				</cfif>
			</cflock>
		</cfif>

		<---  Now Save the Storage --->
		<cfset session[getFlashKey()] = getScope()>
	</cffunction>

	<---  flashExists --->
	<cffunction name="flashExists" output="false" access="public" returntype="boolean" hint="Checks if the flash storage exists and IT HAS DATA to inflate.">
		<cfscript>
    		// Check if session is defined first
    		if( NOT isDefined("session") ) { return false; }
			// Check if storage is set and not empty
			return ( structKeyExists(session, getFlashKey()) AND NOT structIsEmpty(session[getFlashKey()]) );
    	</cfscript>
	</cffunction>

	<---  getFlash --->
	<cffunction name="getFlash" output="false" access="public" returntype="struct" hint="Get the flash storage structure to inflate it.">
		<---  Check if Exists, else return empty struct --->
		<cfif flashExists()>
			<cfreturn session[getFlashKey()]>
		</cfif>

		<cfreturn structnew()>
	</cffunction>

</cfcomponent>
```

As you can see from the implementation, it is very straightforward to create a useful session flash RAM object. You can also get more funky and use some ColdBox internal serializers for any kind of object:

```js
<cfcomponent output="false" extends="coldbox.system.web.flash.AbstractFlashScope" hint="A ColdBox client flash scope">

<-------------------------------------------- CONSTRUCTOR ------------------------------------------>

	<cfscript>
		instance = structnew();
	</cfscript>

	<---  init --->
    <cffunction name="init" output="false" access="public" returntype="ClientFlash" hint="Constructor">
    	<cfargument name="controller" type="coldbox.system.web.Controller" required="true" hint="The ColdBox Controller"/>
    	<cfscript>
    		super.init(arguments.controller);

			// Marshaller
			instance.converter = createObject("component","coldbox.system.core.conversion.ObjectMarshaller").init();
			instance.flashKey = "cbox_flash";

			return this;
    	</cfscript>
    </cffunction>

<-------------------------------------------- PUBLIC ------------------------------------------>

	<---  getFlashKey --->
	<cffunction name="getFlashKey" output="false" access="public" returntype="string" hint="Get the flash key storage used in cluster scope.">
		<cfreturn instance.flashKey>
	</cffunction>

	<---  clearFlash --->
	<cffunction name="clearFlash" output="false" access="public" returntype="void" hint="Clear the flash storage">
		<cfif flashExists()>
			<cfset structDelete(client,getFlashKey())>
		</cfif>
	</cffunction>

	<---  saveFlash --->
	<cffunction name="saveFlash" output="false" access="public" returntype="void" hint="Save the flash storage in preparing to go to the next request">
		<cfset client[getFlashKey()] = instance.converter.serializeObject( getScope() )>
	</cffunction>

	<---  flashExists --->
	<cffunction name="flashExists" output="false" access="public" returntype="boolean" hint="Checks if the flash storage exists and IT HAS DATA to inflate.">
		<cfscript>
    		// Check if session is defined first
    		if( NOT isDefined("client") ) { return false; }
			// Check if storage is set
			return ( structKeyExists(client, getFlashKey()) );
    	</cfscript>
	</cffunction>

	<---  getFlash --->
	<cffunction name="getFlash" output="false" access="public" returntype="struct" hint="Get the flash storage structure to inflate it.">
		<---  Check if Exists, else return empty struct --->
		<cfif flashExists()>
			<cfreturn instance.converter.deserializeObject(client[getFlashKey()])>
		</cfif>

		<cfreturn structnew()>
	</cffunction>

</cfcomponent>
```
