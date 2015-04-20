# HTML base tag

Well, for the base tag you can use either the sesBaseURL setting, if using minimal URL support, or the htmlBaseURL setting if using the index.cfm in the URL. Both of these settings are set by the interceptor at configuration time and available for your usage via the `getSetting()` method.

```js
<base href="#getSetting('htmlBaseURL')#">
<base href="#getSetting('sesBaseURL')#">
```

This tells the browsers how to find your assets when using URL mappings or SES URLs. 
