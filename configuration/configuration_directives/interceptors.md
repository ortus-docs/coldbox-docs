# Interceptors

This is an array of interceptor definitions that you will use to register in your application. The key about this array is that **ORDER** matters. The interceptors will fire in the order that you register them whenever their interception points are announced, so please watch out for this caveat. Each array element is a structure that describes what interceptor to register.

```js
//Register interceptors as an array, we need order
interceptors = [
	//Autowire
	{class="coldbox.system.interceptors.Autowire",
	 properties={useSetterInjection=false}
	},
	//SES
	{class="coldbox.system.interceptors.SES", name="MySES"}
];
```