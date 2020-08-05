# Interceptor Testing

You can test interceptors directly with no need of doing integration testing via the `BaseInterceptorTest`. This way you can unit test interceptors in isolation. All you need to do is the following:

```bash
coldbox create interceptor-test path=interceptors.Deploy points=onCreate --open
```

This testing support class will create your interceptor, and decorate with mocking capabilities via MockBox and mock all the necessary companion objects around interceptors. The following are the objects that are placed in the variables scope for you to use:

* `interceptor` : The target interceptor to test
* `mockController` : A mock ColdBox controller in use by the target interceptor
* `mockRequestService` : A mock request service object
* `mockLogger` : A mock logger class in use by the target interceptor
* `mockLogBox` : A mock LogBox class in use by the target interceptor
* `mockFlash` : A mock flash scope in use by the target interceptor

All of the mock objects are essentially the dependencies of interceptor objects. You have complete control over them as they are already mocked for you.

```javascript
/**
* The base interceptor test case will use the 'interceptor' annotation as the instantiation path to the interceptor
* and then create it, prepare it for mocking, and then place it in the variables scope as 'interceptor'. It is your
* responsibility to update the interceptor annotation instantiation path.
*/
component extends="coldbox.system.testing.BaseInterceptorTest" interceptor="interceptors.test"{

    /*********************************** LIFE CYCLE Methods ***********************************/

    function beforeAll(){
        // interceptor configuration properties, if any
        configProperties = {};
        // init and configure interceptor
        super.setup();
        // we are now ready to test this interceptor
    }

    function afterAll(){
    }

    /*********************************** BDD SUITES ***********************************/

    function run(){

        describe( "interceptors.test", function(){

            it( "should configure correctly", function(){
                interceptor.configure();
            });


        });

    }

}
```

