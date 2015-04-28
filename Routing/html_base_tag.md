# HTML base tag

The `base` tag in HTML allows you to tell the browser what is the base URL for assets in your application. This is great for SES or routed URLs as the base URL is hidden by default.  We definitely recommend using this HTML tag reference as it will simplify your asset retrievals.

```js
<base href="#getSetting('htmlBaseURL')#">
```

If you do not use this tag, then every asset reference must be an absolute URL reference. 
