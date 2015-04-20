# Interceptors

The main purpose of creating ColdBox interceptors is to increase functionality for applications and framework alike, without touching the core functionality or events, and thus encapsulating logic into separate decoupled objects. This pattern wraps itself around a request in specific execution points in which it can process, pre-process, post-process and redirect requests. These interceptors can also be stacked to form interceptor chains that can be executed implicitly for you. These stacked interceptor chains form a chain of separate, declarative-deployable services to an existing web application or framework without incurring any changes to the main application or framework source code. This is a powerful feature that can help developers and framework contributors share and interact with their work. (Read more on [Intercepting Filters](http://www.corej2eepatterns.com/Patterns2ndEd/InterceptingFilter.htm))

![](InterceptorChain.gif)



