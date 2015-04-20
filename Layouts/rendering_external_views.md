# Rendering External Views

So what if I want to render a view outside of my application without using the setting explained above? Well, you use the `renderExternalView()` method.

```js
renderExternalView(string view, [boolean cache='false'], [string cacheTimeout=''], [string cacheLastAccessTimeout=''], [string cacheSuffix=''], [any args])
```

```js
<cfoutput>#renderExternalView(view='/myViewsMapping/tags/footer')#</cfoutput>
```



