# Pathinfo Providers

By default, the URL mapping processor will detect routes by looking at the CGI.PATH_INFO variable. This feature can be useful to set flags for each request based on a URL and then clean or parse the URL to a more generic form to allow for simple route declarations. Uses may include internationalization (i18n) and supporting multiple experiences based on devices such as Desktop, Tablet, Mobile and TV. To modify the URI used by the SES interceptor before route detection occurs simple follow the convention of adding a UDF called `PathInfoProvider()` to your routes configuration file (*config/Routes.cfm*).

The `PathInfoProvider()` UDF is responsibile for returning the string used to match a route. Without this UDF the SES interceptor will simply obtain the URI from the CGI.PATH_INFO variable. 

```js
// Example PathInfoProvider for detecting a mobile request
function PathInfoProvider(Event){
  var rc = Event.getCollection();
  var prc = Event.getCollection(private=true);
  
  local.URI = CGI.PATH_INFO;
  
  if (reFindNoCase('^/m',local.URI) == 0)
  {
    // Does not look like this could be a mobile request...
    return local.URI;
  }
  
  // Mobile Request? Let's find out.
  
  // If the URI is "/m" it is easy to determine that this is a
  // request for the Mobile Homepage.
  if (len(local.URI) == 2)
  {
    prc.mobile = true;
    // Simply return "/" since they want the mobile homepage
    return "/";
  }
  
  // Only continue with our mobile evaluation if we have a slash after
  // our "/m". Without a slash following the /m the route is something
  // else like coldbox.org/makes/cool/stuff
  if (REFindNoCase('^/m/',local.URI) == 1)
  {
    // Looks like we are mobile!
    prc.mobile = true;

    // Remove our "/m/" determination and continue
    // processing for languages...
    local.URI = REReplaceNoCase(local.URI,'^/m/','/');
  }
  
  // The URI starts with an "m" but does not look like
  // a mobile request. So, simply return the URI for normal
  // route detection...
  return local.URI;
} 
```

