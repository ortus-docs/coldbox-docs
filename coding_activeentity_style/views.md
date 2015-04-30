# Views

Here are the views as well, which are pre-created via CommandBox handler creation command.

##index.cfm

```js
<cfoutput>
<h1>Contacts</h1>
#getPlugin("MessageBox").renderit()#
#html.href(href='contacts.editor',text="Create Contact")#
<br><br>
<cfloop array="#prc.contacts#" index="contact">
<div>
	#contact.getLastName()#, #contact.getFirstName()# (#contact.getEmail()#)<br/>
	#html.href(href='contacts.editor.id.#contact.getContactID()#',text="[ Edit ]")#
	#html.href(href='contacts.delete.id.#contact.getContactID()#',text="[ Delete ]",onclick="return confirm('Really Delete?')")#
	<hr>
</div>
</cfloop>
</cfoutput>
```

##editor.cfm

Check out our awesome html helper object. It can even build the entire forms according to the Active Entity object and bind the values for you!


```js
<cfoutput>
<h1>Contact Editor</h1>
#getPlugin("MessageBox").renderit()#
#html.startForm(action="contacts.save")#
	#html.entityFields(entity=prc.contact,fieldwrapper="div")#
	#html.submitButton()# or #html.href(href="contacts",text="Cancel")#
#html.endForm()#
</cfoutput>
```