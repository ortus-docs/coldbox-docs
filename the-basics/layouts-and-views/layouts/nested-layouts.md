# Nested Layouts

You can also wrap layouts within other layouts and get incredible reusability. This is accomplished by using the `renderLayout()` method in the Renderer. As always, refer to the CFC API for the latest method arguments and capabilities.

```javascript
renderLayout([any layout], [any module=''], [any view=''], [struct args={}], [any viewModule=''], [boolean prePostExempt='false'])
```

So if I wanted to wrap my basic layout in a PDF wrapper layout \(`pdf.cfm`\) I could do the following:

```javascript
<cfdocument pagetype="letter" format="pdf">

    <---  Header --->
    <cfdocumentitem type="header">
    <cfoutput>
    <div>
    #dateformat(now(),"MMM DD, YYYY")# at #timeFormat(now(),"full")#
    </div>
    </cfoutput>
    </cfdocumentitem>

    <---  Footer --->
    <cfdocumentitem type="footer">
    <cfoutput>
    <div>
    Page #cfdocument.currentpagenumber# of #cfdocument.totalpagecount#
    </div>
    </cfoutput>
    </cfdocumentitem>

    <---  Main Content via nested layout --->
    #renderLayout(layout="basic")#

</cfdocument>
```

That's it! The `renderLayout()` method is extremely power as it can allow you to not only nest layouts but actually render a-la-carte layout/view combinations also.

