# Ajax Data Binding & More

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


//ColdBox Proxy Code
<cffunction name="getArtists" output="false" access="remote" returntype="Any" hint="Process a remote call and return data/objects back.">
        <cfargument name="ARTISTID" type="numeric" required="false" default="0">
        <cfset var ReturnValue = "" />
        <---  It’s very interesting.. how I am interacting with service-layer, just bypassing controller layer --->
        <cfset ReturnValue = getModel("ArtService").getArtist(argumentCollection=arguments) />

        <cfreturn ReturnValue>
</cffunction>

<cffunction name="getNames" output="false" access="remote" returntype="Array"  hint="Process a remote call and return data/objects back.">
        <cfset var qry  =  "" />
        <---  CFSELECT (bind )  --->
        <cfset var TwoDimensionalArray =  ArrayNew(2) />

        <---  Get Qry Directly from ArtService.cfc --->
        <cfset qry = getModel("ArtService").getArtist() />

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
