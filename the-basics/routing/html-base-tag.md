# HTML Base Tag

The `base` tag in HTML allows you to tell the browser what is the base URL for assets in your application. This is something that is always missed when using frameworks that enable routing.

{% hint style="info" %}
`base` tag defines the base location for links on a page. Relative links within a document \(such as &lt;a href="someplace.html"... or &lt;img src="someimage.jpg"... \) will become relative to the URI specified in the base tag. The base tag must go inside the head element.
{% endhint %}

We definitely recommend using this HTML tag reference as it will simplify your asset retrievals.

```javascript
<base href="#event.getHTMLBaseURL()#">
```

{% hint style="danger" %}
**Caution** If you do not use this tag, then every asset reference must be an absolute URL reference.
{% endhint %}

