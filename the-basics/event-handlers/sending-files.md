# Sending Files

We all need to deliver files to users at one point in time.  ColdBox makes it easy to deliver any type of file even binary files via the [Request Context's](../request-context.md) \(event\) `sendFile()` method.

```javascript
function report( event, rc, prc ){
    var prc.reportFile = reportService.createReport();
    
    event
        .sendFile(
            file = prc.reportFile,
            name = "UserReport.xls",
            deleteFile = true
        )
        .noRender();
}
```

### Method Signature

The API Docs can help you see the entire format of the method: [https://apidocs.ortussolutions.com/coldbox/5.1.4/coldbox/system/web/context/RequestContext.html\#sendFile\(\)](https://apidocs.ortussolutions.com/coldbox/5.1.4/coldbox/system/web/context/RequestContext.html#sendFile%28%29)

The method signature is as follows:

```javascript
/**
 * This method will send a file to the browser or requested HTTP protocol according to arguments.
 * CF11+ Compatibility
 *
 * @file The absolute path to the file or a binary file to send
 * @name The name to send to the browser via content disposition header.  If not provided then the name of the file or a UUID for a binary file will be used
 * @mimeType A valid mime type to use.  If not passed, then we will try to use one according to file type
 * @disposition The browser content disposition (attachment/inline) header
 * @abortAtEnd If true, then this method will do a hard abort, we do not recommend this, prefer the event.noRender() for a graceful abort.
 * @extension Only used for binary files which types are not determined.
 * @deleteFile Delete the file after it has been streamed to the user. Only used if file is not binary.
 */
function sendFile(
    file="",
    name="",
    mimeType="",
    disposition="attachment",
    boolean abortAtEnd="false",
    extension="",
    boolean deleteFile=false
)
```

{% hint style="info" %}
Please note that the `file` argument can be an absolute path or an actual binary file to stream out.
{% endhint %}



