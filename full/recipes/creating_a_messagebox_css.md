# Creating a messagebox css

The MessageBox plugin has been totally revamped to a CSS design. You can override the CSS by configuring a setting in your [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) called messagebox_style_override, which is a boolean variable that defaults to false. You if you want to override, just set it to true.

### Declare the setting
```js
settings.messagebox_style_override = "true";
```

### Messagebox CSS Source
Please override the following css in order to skin your messagebox plugin:

```js
<style>
.cbox_messagebox{
	font-size: 13px;
	font-weight: bold;
}
.cbox_messagebox_info{
	background: #D1E6EF url(/coldbox/system/includes/images/cmsg.gif) no-repeat scroll .5em 50%;
	border: 1px solid #2580B2;
	margin: 0.3em;
	padding: 0pt 1em 0pt 3.5em;
}
.cbox_messagebox_warning{
	background: #FFF2CF url(/coldbox/system/includes/images/wmsg.gif) no-repeat scroll .5em 50%;
	border: 1px solid #2580B2;
	margin: 0.3em;
	padding: 0pt 1em 0pt 3.5em;
}
.cbox_messagebox_error{
	background: #FFFFE0 url(/coldbox/system/includes/images/emsg.gif) no-repeat scroll .5em 50%;
	border: 1px solid #2580B2;
	margin: 0.3em;
	padding: 0pt 1em 0pt 3.5em;
}
</style>
```
