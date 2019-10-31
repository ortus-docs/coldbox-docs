# Module Inception

![](https://github.com/ortus-docs/coldbox-docs/blob/v4.x/full/images/Modules.png)

ColdBox 4 allows you to nest modules within modules up to the Nth degree. You can package a module with other modules that can even contain other modules within them. It really opens a great opportunity for better packaging, delivery and a further break from monolithic applications. To use, just create a `modules` folder in your module and drop the modules there as well. You can also just create a `box.json` in the root of your module and let CommandBox manage your module dependencies. Here is an example of the ColdBox ORM Module:

```javascript
{
    "name"         : "ColdBox ORM Extensions",
    "version"    : "1.0.3",
    "author"     : "Luis Majano <lmajano@ortussolutions.com",
    "slug"        : "cborm",
    "type"        : "modules",
    "homepage"    : "http://www.coldbox.org",
    "documentation"        : "http://wiki.coldbox.org",
    "repository"        : { "type" : "git", "url" : "https://github.com/ColdBox/cbox-cborm" },
    "shortDescription"    : "Enhances the ColdFusion ORM with tons of utilities.",
    "license"            : [
        { "type" : "Apache2", "url" : "http://www.apache.org/licenses/LICENSE-2.0.html" }
    ],
    "contributors"        : [
        "Brad Wood <bdw429s@gmail.com>", "Curt Gratz <gratz@computerknowhow.com>", "Joel Watson <existdissolve@gmail.com>"
    ],
    "dependencies"     :{
        "cbvalidation" : "1.0.2"
    },
    "ignore":[
        "**/.*",
        "tests",
        "apidocs",
        "*/.md"
    ]
}
```

