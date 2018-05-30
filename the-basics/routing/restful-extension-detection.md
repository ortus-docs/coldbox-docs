# RESTFul Extension Detection

## Usage

ColdBox allows you to detect incoming extensions from incoming paths automatically for you.  This is great for building multi-type responses or to just create virtual extensions for events.

```text
http://localhost/users
http://localhost/users.json
http://localhost/users.xml
http://localhost/users.pdf
http://localhost/users.html    
```

If an extension is detected in the incoming URL, ColdBox will grab it and store it in the request collection \(`RC`\) as the variable `format`.  If there is no extension, then `rc.format` will not be stored and thus will not exist.

```text
http://localhost/users => rc.format is null, does not exist
http://localhost/users.json => rc.format = json
http://localhost/users.xml => rc.format = xml
http://localhost/users.pdf => rc.format = pdf
http://localhost/users.html => rc.format = html
```

## Configuration

You can configure the extension detection using the following configuration methods:

| **Method** | **Description** |
| --- | --- |
| `setExtensionDetection( boolean )` | By default ColdBox detects URL extensions like `json, xml, html, pdf` which can allow you to build awesome RESTful web services. Default is **true**. |
| `setValidExtensions( list )` | Tell the interceptor what valid extensions your application can listen to. By default it listens to: `json, jsont, xml, cfm, cfml, html, htm, rss, pdf` |
| `setThrowOnInvalidExtensions( boolean )` | By default ColdBox does not throw an exception when an invalid extension is detected. If **true**, then the interceptor will throw a 406 Invalid Requested Format Extension: {extension} exception. Default is **false**. |

{% hint style="warning" %}
Please note that if you have set to throw exceptions if an invalid extension is detected then a 406 exception will be thrown.
{% endhint %}

