# Basic Layouts

```markup
<cfoutput>
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>#prc.title#</title>
</head>
<body>
  <!--- Header: Direct Render --->
  #view( view='tags/header')#

  <div id="content">
    <!--- Render set view --->
    #view()#
  </div>

  #view( view='tags/footer' )#
</body>
</html>
</cfoutput>
```
