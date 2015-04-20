# Implicit Layout/View Declarations

Now that we have seen what layouts and views are, where they are located and some samples, let's dig deeper. There is a special section in the configuration file of an application just for layouts and views. This section is the layouts section and you can find much more in detail information in the [Configuration CFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) guide. Let's look at a sample declaration below:

```js
//Register Layouts
layouts = [
	{ name = "login",
 	  file = "Layout.tester.cfm",
	  views = "vwLogin,test",
	  folders = "tags,pdf/single"
	}
];
```

This setting allows you to implicitly define layout to view/folder assignments without the need of programmatically doing it. This is a time saver and a nice way to pre-define how certain views will be rendered. Let's see more examples:

```js
//Register Layouts
layouts = [
	{ name = "Popup",
 	  file = "Layout.Popup.cfm",
	  views = "login,test",
	  folders = "^tags,pdf/single"
	},
	{ name = "help",
	  file = "layout.help.cfm",
	  folders = "^help"
	}
];
```

In the sample, we declare the layout named popup and points to the file Layout.Popup.cfm. We can then assign it to views or folders:
* Views : The views to assign to this layout (no cfm extension)
* Folders : The folders and its children to assign to this layout. (regex ok)

This is cool, we can tell the framework that some views and some folders should be rendered within a specific layout. Wow, this opens the possibility of creating nested applications that need different rendering schemas! Is it that easy? Yes it is!
