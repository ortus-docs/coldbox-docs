# Techniques

There are two techniques when interfacing with the proxy:

1. Calling the `process()` method or calling a method that uses the process() method to execute an event within your application.
2. Calling a method in a proxy object that get's data from a service layer directly via the object retrieval methods.

Which one you use, well depends on your requirements and interactivity. If you have a service layer that can provide you with data directly with no request flow or security, then use method 2, else maybe method 1 is your cup of tea.

### How?

The greatest question of all. Well, in the distro application template you will find a basic coldboxproxy.cfc that can be used for remote operations. The inherent basics here is that you have a component that extends the base coldbox proxy class: extends=coldbox.system.extras.ColdBoxProxy. Below is this simple cfc

```js
<cfcomponent name="coldboxproxy" output="false" extends="coldbox.system.extras.ColdboxProxy">

	<---  You can override this method if you want to intercept before and after. --->
	<cffunction name="process" output="false" access="remote" returntype="any" hint="Process a remote call and return data/objects back.">
		<cfset var results = "">
		
		<---  Anything before --->
		
		<---  Call the actual proxy --->
		<cfset results = super.process(argumentCollection=arguments)>
		
		<---  Anything after --->
		
		<cfreturn results>
	</cffunction>
</cfcomponent>
```

You can use this simple proxy to call events via the process method. How? Here is a javascript sample using cfajaxproxy:

```js
<---  Declare the CF Ajax Proxy HERE --->
<cfajaxproxy cfc="coldboxproxy.cfc" jsclassname="cbProxy">

<script type="text/javascript">
var getArtists = function(){
	var cbox = new cbProxy();
      // Setting a callback handler for the proxy automatically makes
      // the proxy's calls asynchronous.
      cbox.setCallbackHandler(populateArtists);
      cbox.setErrorHandler(myErrorHandler);
  	  // The proxy getArtists function represents the CFC
  	  // getArtists function.
      cbox.process(event:'artists.list');
 }
</script>
```

This calls the process method with an argument of *event = artists.list*. You can extrapolate how to do more stuff from here. So let's do another sample, let's add a method to our proxy so we can do data binding.

```js
<cffunction name="getArtists" output="false" access="remote" returntype="Any" hint="Process a remote call and return data/objects back.">
	<cfargument name="ARTISTID" type="numeric" required="false" default="0">
	<cfset var ReturnValue = "" />
	<---  Its very iteresting.. how I am intracting with service-layer, just bypassing controller layer --->
	<cfset ReturnValue = getBean("ArtService").getArtist(argumentCollection=arguments) />
	
	<cfreturn ReturnValue>
</cffunction>

<cffunction name="getNames" output="false" access="remote" returntype="Any"  hint="Process a remote call and return data/objects back.">
	<cfset var qry  =  "" />
	<---  CFSELECT (bind )  --->
	<cfset var TwoDimensionalArray =  ArrayNew(2) />
	
	<---  Get Qry Directly from ArtService.cfc --->
	<cfset qry = getBean("ArtService").getArtist() />
	
	<cfset TwoDimensionalArray[1][1] = '0' />
	<cfset TwoDimensionalArray[1][2] = 'Please select' />
	
	<cfloop query="qry">
		<cfset TwoDimensionalArray[qry.CurrentRow + 1][1] = trim(qry.ARTISTID)>
		<cfset TwoDimensionalArray[qry.CurrentRow + 1][2] = trim(qry.FIRSTNAME & chr(32) & qry.LASTNAME)>
	</cfloop>

	<---  Anything after --->
	<cfreturn TwoDimensionalArray>
</cffunction>
```

The two methods above go directly to the service layer by using the convenience method of getBean(). Then some js calls using cfajaxproxy:

```js
<script language="javascript">
var getArtistDetails = function(id){
  document.getElementById('empData').innerHTML = 'Please wait! loading data...<br><br><img src="css/8-1.gif">';	
  var e = new cbProxy();
  e.setCallbackHandler(populateArtistDetails);
  e.setErrorHandler(myErrorHandler);
  // This time, pass the employee name to the getArtists CFC
  // function.
  e.getArtists(id);
}
</script>
```

The last sample is another method added to our proxy object that get's some content:

```js
<cffunction name="dspTab2" output="false" access="remote" returntype="any" hint="Process a remote call and return data/objects back.">
	<cfset var results = "" />
	<---  call even handler to get query data etc --->
	<cfset arguments["event"] = "ehAjax.dspTab2">

	<---  Call the actual proxy --->
	<cfset results = super.process(argumentCollection=arguments)>
	
	<cfreturn results>
</cffunction>
```

This simple method executes the process() method on its base class, our coldbox proxy, and then returns the results in whatever format they come in. So our ehAjax.dspTab2 handler can look something like this:

```js
<---  tab2 content --->
<cffunction name="dspTab2" access="public" returntype="any" output="false">
	<cfargument name="Event" type="coldbox.system.beans.requestContext">
	<---  send back to proxy --->
	<cfset event.renderData(type="plain",data=renderView('ajax/vwTab2')) />
</cffunction>
```

How cool is that, we just used the renderData method to render a view remotely. How COOL is that? You can see from this example, that ColdBox truly offers flexibility and great interaction with any remote caller.

Some more samples below:

```js
//CFGRID data binding
<cfform>
    <cfgrid name = "FirstGrid" 
	    format="html"
	    font="Tahoma" 
	    fontsize="12"
	    pageSize="10"
	    width="100%"	
    	    preservePageOnSort="yes"		
	    bind="cfc:coldboxproxy.getAllArtist({cfgridpage},{cfgridpagesize},{cfgridsortcolumn},{cfgridsortdirection})" >
	
         <cfgridcolumn name="ARTISTID"	display="true" header="ARTIST ID"/>
         <cfgridcolumn name="ARTNAME"	display="true" header="Name"/>
         <cfgridcolumn name="FIRSTNAME"	display="true" header="First Name"/>
         <cfgridcolumn name="LASTNAME"	display="true" header="Last Name"/>
         <cfgridcolumn name="EMAIL"	display="true" header="Email"/>
    </cfgrid>
</cfform>

//AUTO SUGGEST

<cfform>
<h2>CFINPUT Auto-Suggest:</h2>
<p>Type <strong>Ma</strong> or <strong>Ch</strong> </p>
<cfinput type="text"
      name="employeename"
      autosuggestminlength="2"
      autosuggest="cfc:coldboxproxy.SearchName({cfautosuggestvalue})">
</cfform>

<---   CF8 cfgrid using ajax cfform is mendatory for using ajax stuff --->
<h1>cfselect bind to remote cfc:</h1>
<cfoutput>
<cfform name="mycfform">
    <---  The States selector.  The bindonload attribute is required to fill the selector. --->
    <cfselect name="ArtistID" bind="cfc:coldboxproxyS.getNames()" bindonload="true">
        <option name="0">--select--</option>
    </cfselect>
</cfform>
</cfoutput>
```


