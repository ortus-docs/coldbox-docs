# Funky Viewlets

ColdBox also allows you to have Funky Viewlets and yes that is a real technical term. Funky viewlets allows you to pass arguments to the `runEvent()` method calls so you can simulate normal method calls and pass extra arguments to the events. So let's make our previous viewlet into a funky viewlet:

```js
function userinfo(event,rc,prc,boolean widget=false){

	// place data in prc and prefix it to avoid collisions
	prc.userinfo_qData = userService.getUserInfo();

	// render out content as widget or normal procedures
	if( arguments.widget ){
		return renderView("viewlets/userinfo");
	}
	event.setView('viewelts/userinfo');
}
```

What have I done? I have added a custom argument to my action signature: bolean widget=false. Then I split the rendering depending on this incoming argument. WOW! So how do I call my funky viewlet?

```js
<div id="leftbar">
#runEvent(event='viewlets.userinfo',prepostExempt=true,eventArguments={widget=true})#
</div>
```

That easy! Just add another argument called eventArguments and now you can pass extra arguments to your event actions and cook up some funky viewlets!

