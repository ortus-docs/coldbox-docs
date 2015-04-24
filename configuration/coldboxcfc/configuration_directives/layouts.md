# Layouts

The layouts array element is used to define implicit associations between layouts and views/folders, this does not mean that you need to register ALL your layouts. This is a convenience for pairing them, we are in a conventions framework remember. 

Before any renderings occur or lookups, the framework will check this array of associations to try and match in what layout a view should be rendered in. It is also used to create aliases for layouts so you can use aliases in your code instead of the real file name and locations.

```js
//Register Layouts
layouts = [
    { 
        // The alias of a layout
        name="",
        // The layout file
        file="",
        // A list of view names to render within this layout
        views="",
        // A list of regex names to match a view
        folders=""
    }

    // Examples
	{ name="tester",file="Layout.tester.cfm",views="vwLogin,test",folders="tags,pdf/single"	},
	{ name="login",file="Login.cfm",folders="^admin/security"}
];
```

