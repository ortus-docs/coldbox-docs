# Interceptor Testing

You can now test interceptors directly with no need of doing integration testing. This way you can unit test interceptors directly very very easily. All you need to do is the following:

1. Create a test class that inherits from coldbox.system.testing.BaseInterceptorTest
2. Create a component annotation called interceptor that equals the full path of the handler CFC to target for unit testing
3. Create an optional strcuture variable in the variables scope called configProperties in the setup() method before your super.setup() method, if you would like to test the interceptor with your configuration properties structure.

This testing support class will create your interceptor, and decorate with mocking capabilities via MockBox and mock all the necessary companion objects around interceptors. The following are the objects that are placed in the variables scope for you to use:

* interceptor : The target interceptor to test
* mockController : A mock ColdBox controller in use by the target interceptor
* mockRequestService : A mock request service object
* mockLogger : A mock logger class in use by the target interceptor
* mockLogBox : A mock LogBox class in use by the target interceptor
* mockFlash : A mock flash scope in use by the target interceptor

All of the mock objects are essentially the dependencies of interceptor objects. You have complete control over them as they are already mocked for you.

Basic Setup

```js
component extends="coldbox.system.testing.BaseInterceptorTest" interceptor="myApp.interceptors.Security"{

  // Just create test methods, no need to use the setup() method unless you want to:
  function setup(){
      configProperties = {
         roles = "admin,user,moderator",
         security = "active"
      };
      super.setup();
      mockSecurityService = getMockBox().createEmptyMock("myapp.model.SecurityService");
      // wire in my mock dependencies as this interceptor already has mocking capabilities
      interceptor.$property("securityService","variables",mockSecurityService);

      // we are now ready to test this interceptor
  }

}
```
Real Example

```js
<cfcomponent extends="coldbox.system.testing.BaseInterceptorTest" interceptor="coldbox.system.interceptors.Deploy">
<cfscript>

function setup(){
	super.setup();
	
	// mocks
	mockController.$("getAppRootPath",expandPath('/coldbox/testharness'));
	interceptor.setProperty("tagFile","config/.deploy_tag");
	interceptor.$("locateFilePath","config/.deploy_tag").$("setSetting");
	
}

function testConfigure(){
	interceptor.configure();
}

function testAfterAspectsLoad(){
	mockLogger.$("info");
	interceptor.afterAspectsLoad(getMockRequestContext());
}

function testPostProcess(){

	//mocks
	mockController.$("getColdboxInitiated",true).$("setColdboxInitiated").$("setAspectsInitiated");
	testDate = now();
	mockLogger.$("info").$("error");
	interceptor.$property("tagFilePath","instance",'config/.deployTag');
	interceptor.$property("deployCommandObject","instance",'');
	
	// Test no setting
	interceptor.$("settingExists",false).$("configure");
	
	interceptor.postProcess(getMockRequestContext());
	assertEquals( 1, arrayLen(interceptor.$callLog().configure) );
	
	// Test setting exists but same date
	interceptor.$("getSetting",testDate).$("fileLastModified",testDate).$("settingExists",true);
	interceptor.postProcess(getMockRequestContext());
	assertEquals( 0, arrayLen(mockController.$callLog().setColdboxInitiated) );
	
	// Test it works
	interceptor.$("getSetting",testDate).$("fileLastModified",testDate+10).$("settingExists",true);
	interceptor.postProcess(getMockRequestContext());
	assertEquals( 1, arrayLen(mockController.$callLog().setColdboxInitiated) );
	
	
}	
</cfscript>	
</cfcomponent>
```

