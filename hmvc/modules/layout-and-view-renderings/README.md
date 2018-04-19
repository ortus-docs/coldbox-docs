# Layout and View Renderings

The ColdBox rendering engine has been adapted to support module renderings and also to support multiple discovery algorithms when rendering module layouts and views. When you declare a module you can declare two of its public properties to determine how rendering overrides occur. These properties are:

| Property | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| viewParentLookup | boolean | false | true | If true, coldbox checks for views in the parent overrides first, then in the module. If false, coldbox checks for views in the module first, then the parent. |
| layoutParentLookup | boolean | false | true | If true, coldbox checks for layouts in the parent overrides first, then in the module. If false, coldbox checks for layouts in the module first, then the parent. |

