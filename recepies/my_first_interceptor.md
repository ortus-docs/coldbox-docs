# My First Interceptor

### Introduction

This hands on guide will show you how to quickly create a ColdBox interceptor. If you are still not that familiar with ColdBox interceptors, I suggest you read the Interceptors Guide. This guide is just a quick hands on that will show you the following:

1. How to declare your interceptor
2. How to create your interceptor
3. Creating some methods

### How to declare you interceptor

In order to use an interceptor you must first declare it on your ColdBox configuration file under the Interceptors array element. Each interceptor declaration is a structure with the following elements:

* class : The instantiation path of the Interceptor CFC
* name : The unique name of the CFC, if not passed, it uses the name of the CFC
* properties : A structure of configuration properties for the CFC

```js
interceptors = [
  {class="model.interceptors.xmlInjector", properites={
   xmlFile = "config/myCustomXML.xml",
   varName = "myCustomXML" }
  }
];
```

> **Note** Interceptors can also be registered at runtime by using the provided API in the interceptor service object.