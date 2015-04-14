# Tips And Tricks

Some of the methods below will help you when dealing with views and their renderings.

#### I don't want to render anything
```js
<cfset event.noRender()>
```
#### I don't want a coldbox debugging panel attached to this view
```js
<cfset event.showdebugpanel("false")>
```
#### Is this a ColdBox proxy request
```js
<cfset event.isProxyRequest()>
```
#### Am I in an ses application
```js
<cfset event.isSES()>
```
#### Am I in an ajax call
```js
<cfset event.isAjax()>
```
#### Render an event as a viewlet
```js
<cfoutput>#runEvent(event="nav:main.leftnav",prepostExempt=true)</cfoutput>
```
#### What is the current layout?
```js
<cfset event.getCurrentLayout()>
```
#### What is the current view
```js
<cfset event.getCurrentView()>
```