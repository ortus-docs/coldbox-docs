# Rendering Data

ColdBox provides you with a utility method called `renderData()` located in the event object (request context) that can be used from your event handlers. This method will provide you with the ability to render data back to the browser or to a remote caller without rendering or creating views. The framework will take care of marshalling (converting) the data for you and return it back. This is an incredible tool to use when doing AJAX or remote interactions from flex or air or when building RESTful web services. You can also do your own conversions if you like, but it would be your responsibility to format the data correctly, set the correct headers, econding and content type. We call these view transformers and can be done very easily!

```js
public any renderData([any type='HTML'], any data, [any contentType=''], [any encoding='utf-8'], [numeric statusCode='200'], [any statusText=''], [any location=''], [any jsonCallback=''], [any jsonQueryFormat='query'], [boolean jsonAsText='false'], [any xmlColumnList=''], [boolean xmlUseCDATA='false'], [any xmlListDelimiter=','], [any xmlRootName=''], [struct pdfArgs='[runtime expression]'], [any formats=''], [any formatsView=''])
```

Out of the box ColdBox can marshall data (structs,queries,arrays,complex or even ORM entities) into the following output formats:
* XML
* JSON
* JSONP
* HTML
* TEXT
* PDF
* WDDX
* CUSTOM

