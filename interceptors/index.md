# Interceptors

Interceptors are CFC listeners that react on incoming events.  Events can be announced by the core framework or your application.  These interceptors can also be stacked to form interceptor chains that can be executed implicitly for you. These stacked interceptor chains form a chain of separate, declarative-deployable services to an existing web application without incurring any changes to the main application or framework source code. This is a powerful feature that can help developers and framework contributors share and interact with their work. (Read more on [Intercepting Filters](http://www.corej2eepatterns.com/Patterns2ndEd/InterceptingFilter.htm))

![](InterceptorChain.gif)



