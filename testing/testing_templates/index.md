# Testing Templates

![](../../TestingTemplate.png)

The ColdBox download comes with an application templates folder or the CF Builder tools also generate ColdBox applications with a test directory that includes all you need to begin your testing adventure. So let's start by how we have partitioned these templates so we can start testing.

```js
|Advanced
|--+test
    |--+ integration ( integration tests go here )   
    |--+ mocks ( Manual mock objects or data go here )
    |--+ modules ( Testing for modules goes here )
    |--+ resources
       |--+ RemoteFacade.cfc ( To connect Eclipse to your Application )
       |--+ HttpAntRunner.cfc ( To connect ANT to your Application )
    |--+ unit ( Unit tests for model objects go here )
    + Application.cfc (Your testing application file)
    + test.xml (ANT script to automate your tests)
    + TestSuite.cfm (A test suite to run all your tests)
```

This test template or test harness includes folders for major test classes you will be creating, Eclipse & ANT connection resources, ANT scripts and so much more. This is a great starting point for your application testing adventure. The most important piece of your test harness is the Application.cfc file. Please note that your testing harness must have a different Application space than your normal ColdBox application. You do not want your tests to alter the real application and vice versa. So in this Application.cfc you will try an mimic all the real application's session, client, scopes, ORM, S3, settings, etc. The trickiest part to setup is ORM integration. You have to make sure that your paths and entities can be located by your tests which run in a separate application space.

Basic Application.cfc: 

```js
<cfcomponent output="false">
	<cfset this.name = "ColdBoxTestingSuite" & hash(getCurrentTemplatePath())> 
	<cfset this.sessionManagement = true>
	<cfset this.sessionTimeout = createTimeSpan(0,0,30,0)>
	<cfset this.setClientCookies = true>
	
</cfcomponent>
```