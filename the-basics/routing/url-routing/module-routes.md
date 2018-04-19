# Module Routes

You can use the `addModuleRoutes()` to define a-la-carte module entry points. By convention, all module's declared entry point in their configuration file is registered for you. However, you can use this manual method approach to create aliases or funky stuff:

```javascript
addModuleRoutes( pattern="/blog", module="blog" );
addModuleRoutes( pattern="/news", module="blog" );
```

