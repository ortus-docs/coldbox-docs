# HTML base tag

The `base` tag in HTML allows you to tell the browser what is the base URL for assets in your application.

> `base` tag defines the base location for links on a page. Relative links within a document \(such as &lt;a href="someplace.html"... or &lt;img src="someimage.jpg"... \) will become relative to the URI specified in the base tag. The base tag must go inside the head element.

We definitely recommend using this HTML tag reference as it will simplify your asset retrievals.

```javascript
<base href="#getSetting('htmlBaseURL')#">
```

> **Caution** If you do not use this tag, then every asset reference must be an absolute URL reference.

