# Content Variable Views

A content variable is a variable that contains HTML/XML or any kind of visual content that can easily be rendered anywhere. You can easily do this by leveraging inline rendering and placing the content into a variable, usually in an event handler:

```javascript
function home(event,rc,prc){

    // render some content variables with funky arguments
    prc.sideColumn = renderView(view='tags/sideColumn',cache=true,cacheTimeout=10);

    // set view
    event.setView('general/home');
}
```

So how do I render it?

```javascript
<div id="content">
  <div id="leftColumn">
  <cfoutput>#prc.sideColumn#</cfoutput>
  </div>

  <div id="mainView">
  <cfoutput>#renderView()#</cfoutput>
  </div>
</div>
```

Another example, is what if we do not know if the content variable will actually exist? How can we do this? Well, we use the event object for this and its magic getValue\(\) method.

```javascript
<div id="content">
  <div id="leftColumn">
  <cfoutput>#event.getValue(name='sideColumn',defaultValue='',private=true)#</cfoutput>
  </div>

  <div id="mainView">
  <cfoutput>#renderView()#</cfoutput>
  </div>
</div>
```

So now, if no content variable exists, an empty string will be rendered.

> **Important** String manipulation in Java relies on immutabile structures, so performance penalities might ensue. If you will be doing a lot of string manipulation, concatenation or rendering, try to leverage native java objects: StringBuilder or StringBuffer

