# Rendering External Views

So what if I want to render a view outside of my application without using the setting explained above? Well, you use the `renderExternalView()` method.

```javascript
<cfoutput>#renderExternalView(view='/myViewsMapping/tags/footer')#</cfoutput>
```

