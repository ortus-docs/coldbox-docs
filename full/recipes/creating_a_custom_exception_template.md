# Creating a Custom exception template

This is a sample for creating a custom exception template to use with ColdBox. You will have to set the CustomErrorTemplate setting in the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) in order to use this feature. If it is not used, then ColdBox will use its own default exception template for exceptions.

### How to enable it?

```js
coldbox.customErrorTemplate = "views/mycustomException.cfm";
```

For the custom error template, you need to set the relative path or mapping path to the template that will be shown when exceptions occurr. An important detail about it, is that it does not get rendered as a normal layout/view, it is a standalone ColdFusion template. Why? Well, we have an exception so doing more complex rendering can produce even more errors. So try to be as simple and direct as possible.

Usage
You will have the ExceptionBean object available in the request collection for you to use and display a custom error message in your template. You can see all of the exception bean's methods and properties by [visiting the API](http://www.coldbox.org/api). Below is a listing of the available properties and methods that you can use within a custom error template.

Available Properties
* Event : The event object with the ExceptionBean

Integration Methods
* getController() : To get the coldbox controller.

You can then go ahead and code your custom exception template:

```js
<cfset exceptionBean = event.getValue("ExceptionBean") />

<h3>An Unhandled Exception Occurred</h3>

<cfoutput>
<table>
	<tr>
		<td colspan="2">An unhandled exception has occurred. Please look at the diagnostic information below:</td>
	</tr>
	<tr>
		<td valign="top"><strong>Type</strong></td>
		<td valign="top">#exceptionBean.getType()#</td>
	</tr>
	<tr>
		<td valign="top"><strong>Message</strong></td>
		<td valign="top">#exceptionBean.getMessage()#</td>
	</tr>
	<tr>
		<td valign="top"><strong>Detail</strong></td>
		<td valign="top">#exceptionBean.getDetail()#</td>
	</tr>
	<tr>
		<td valign="top"><strong>Extended Info</strong></td>
		<td valign="top">#exceptionBean.getExtendedInfo()#</td>
	</tr>
	<tr>
		<td valign="top"><strong>Message</strong></td>
		<td valign="top">#exceptionBean.getMessage()#</td>
	</tr>
	<tr>
		<td valign="top"><strong>Tag Context</strong></td>
		<td valign="top">
	       <cfset tagCtxArr = exceptionBean.getTagContext() />
	       <cfloop index="i" from="1" to="#ArrayLen(tagCtxArr)#">
	               <cfset tagCtx = tagCtxArr[i] />
	               #tagCtx['template']# (#tagCtx['line']#)<br>
	       </cfloop>
		</td>
	</tr>
	<tr>
		<td valign="top"><strong>Stack Trace</strong></td>
		<td valign="top">#exceptionBean.getStackTrace()#</td>
	</tr>
</table>
</cfoutput>
```
> **Important** This template does not get generated as a normal view. Therefore, in order to interact with the framework from the custom exception template you will have to go via the ColdBox controller (Full Syntax). For example, if you want to use a resourcebundle plugin to get an error message string you would do a full syntax like so:

```js
<cfoutput>#getController().getPlugin("ResourceBundle").getResource("errormessage")# : </cfoutput>
```

As you can see, you get a reference to the controller via the getController() method. You can then do whatever you feel like doing.

### Conclusion
And that is all folks, easy as pie. This setting goes great with creating your own exception handler also. So you can actually decide what to log or what to do with exceptions. Look at My First Custom Exception Handler


