# Composed Properties

![Event Handlers](../images/EventHandlers.jpg)

It is imperative that you realize that there is a great object model behind every event handler controller that will enable you to do your work more efficiently. The following are the composed properties every event handler has in their <code>variables</code> scope, you do not need to do anything to retreive them, they are already there :)

* **cachebox** : A reference to the CacheBox library (<code>coldbox.system.cache.CacheFactory</code>)
* **controller** : A reference to the Application Controller (<code>coldbox.system.web.Controller</code>)
* **flash** : A reference to the Application's Flash implementation. (<code>coldbox.system.web.flash.AbstractFlashScope</code>)
* **LogBox** : A reference to the application LogBox (<code>coldbox.system.logging.LogBox</code>)
* **Log** : A pre-configured logging logger object (<code>coldbox.system.logging.Logger</code>)
* **wirebox** : A reference to the application WireBox Injector (<code>coldbox.system.ioc.Injector</code>)
* **$super** : A reference to the virtual super class (<code>coldbox.system.EventHandler</code>) Only if using the non-inheritance approach

