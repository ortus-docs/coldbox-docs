# Basic Layouts

```html
<cfoutput>
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>#prc.title#</title>
</head>
<body>
  <!--- Header: Direct Render --->
  #renderView( view='tags/header')#

  <div id="content">
    <!--- Render set view --->
    #renderView()#
  </div>

  #renderView( view='tags/footer' )#
</body>
</html>
</cfoutput>
```

