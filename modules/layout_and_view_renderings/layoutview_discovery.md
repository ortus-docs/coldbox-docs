# Layout/View Discovery

The default order of overrides ColdBox offers is both viewParentLookup & *layoutParentLookup* to true. This means that if the layout or view requested to be rendered by a module exists in the overrides section of the host application, then the host application's layout or view will be rendered instead. Let's investigate the order of discover:

![](../../ModulesViewLookupTrue.jpg)

