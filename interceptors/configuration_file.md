# Configuration File

```js
interceptors = [
	{class="coldbox.system.interceptors.SES", properties={}},
	{class="interceptors.RequestTrim", properties={trimAll=true}},
	{class="coldbox.system.interceptors.Security", name="AppSecurity", properties={
		rulesSource 	= "model",
		rulesModel		= "securityRuleService@cb",
		rulesModelMethod = "getSecurityRules",
		validatorModel = "securityService@cb"}
	}}
];
```