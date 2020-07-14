# Restricting Execution

You can restrict the execution of an interception point by using the `eventpattern` annotation. This annotation will be placed in the function and the value is a regular expression that the interceptor service uses to match against the incoming event. If the regex matches, the interception function executes, else it skips it. This is a great way for you to create interceptors that only fire not only for the specific interception point you want, but also on the specific incoming event.

```javascript
// only execute for the admin package
void function preProcess(event,struct data) eventPattern="^admin\."{}

// only execute for the blog module
void function preProcess(event,struct data) eventPattern="^blog:"{}

// only execute for the blog module and the home handler
void function preProcess(event,struct data) eventPattern="^blog:home\."{}

// only execute for the blog, forums, and shop modules
void function preProcess(event,struct data) eventPattern="^(blog|forum|shop):"{}
```

