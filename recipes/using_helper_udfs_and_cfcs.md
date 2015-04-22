# Using Helper UDF's and CFC's

### Introduction

This quick guide shows you how easily you can create a set of helper UDF templates or CFC's and bring them in to your layouts, views, plugins, handlers and interceptros for usage. So why would you want to do this? Well, re usability and separation. With ColdBox you have the added functionality that the way views and layouts are rendered you inherit various methods that are really useful. One of these methods is the includeUDF() method. Below is a snapshot of this method:

* includeUDF(string udflibrary)

So what does this method do? This method takes in a path to a udf library to load. This library can be a .cfm template or an actual .cfc template. The method will then try to look in your application for it first. If it cannot find it, it will then try to expand the incoming path and locate it. This makes it so easy to create a helpers directory in your application root and just use it.

> **Info** You do not need a .cfc or .cfm extension passed as it will locate it for you. 

### The Helpers

Let's start off by creating a helpers directory in your application root and placing a helper there called: dateutil.cfm or dateutil.cfc. Below is a sample content, of course you could add more:

Template Sample

```js
//template
<---  formatDate --->
<cffunction name="formatDate" output="false" access="public" returntype="string" hint="Format dates">
	<cfargument name="datestr" type="string" required="true" default="" hint="The date string"/>
	<cfargument name="format" type="string" required="true" default="full" hint="the formating: full, short, medium"/>
	<cfscript>
		if( not isDate(arguments.datestr) ){
			return arguments.datestr;
		}
		if( not reFindNoCase("^(short|medium|full)$",arguments.format) ){
			arguments.format = "full";
		}
		return dateFormat(arguments.datestr,arguments.format);
	</cfscript>
</cffunction>
```

CFC Sample

```js
//cfc
<cfcomponent name="dateUtil" output="false">

<---  formatDate --->
<cffunction name="formatDate" output="false" access="public" returntype="string" hint="Format dates">
	<cfargument name="datestr" type="string" required="true" default="" hint="The date string"/>
	<cfargument name="format" type="string" required="true" default="full" hint="the formating: full, short, medium"/>
	<cfscript>
		if( not isDate(arguments.datestr) ){
			return arguments.datestr;
		}
		if( not reFindNoCase("^(short|medium|full)$",arguments.format) ){
			arguments.format = "full";
		}
		return dateFormat(arguments.datestr,arguments.format);
	</cfscript>
</cffunction>

</cfcomponent>
```

Ok, you have created your first helper. All it does is have the ability to format dates for printing. These are centrally located and can be used throughout any part of the framework easily. So let's use them.

### Helper Usage
We now created the helpers, how do we use them? Using our includeUDF() method. So let's look at a sample view code that will need this helper.

```js
<---< Include Helper --->
<cfset includeUDF('helpers/dateutil')>

<div>
Welcome Luis, your last login was done on #formatdate(rc.oUser.getLoginDate())#
</div>

<p>The current date is #formatdate(now(),"short")#</p>
```
That's it! By using the include UDF and the path to the library to load, ColdBox will take care of the rest.
