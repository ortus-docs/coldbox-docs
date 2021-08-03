# Rendering External Views

So what if I want to render a view outside of my application without using the setting explained above? Well, you use the `externalView()` method.

```javascript
<cfoutput>#externalView(view='/myViewsMapping/tags/footer')#</cfoutput>
```

{% hint style="info" %}
If you are using ColdBox 6.4 or older, you will want to use the `renderExternalView()` method name. In ColdBox 6.5.2+, `renderExternalView()` was deprecated in favor of the new `externalView()` method.
{% endhint %}
