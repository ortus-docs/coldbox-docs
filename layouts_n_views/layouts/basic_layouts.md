# Basic Layouts

```js
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
 "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1" />
<title><cfoutput>#rc.title#</cfoutput></title>

<---< SES base --->
<cfoutput>
<base href="#getSetting('htmlBaseURL')#">
</cfoutput>
</head>

<body>
<cfoutput>

<---< Render a header view here and cache it for 30 minutes.--->
#renderView(view='tags/header',cache=true,cacheTimeout='30')#

<div id="content">
       <---< Use a plugin helper to render a UI messagebox --->
       #getPlugin("messagebox").renderit()#
	   
       <---< Render the set view below: NO Arguments --->
       #renderView()#
</div>

<---< Render the footer and also cache it --->
#renderView(view='tags/footer',cache=true,cacheTimeout='30')#
</cfoutput>
</body>
</html>
```

