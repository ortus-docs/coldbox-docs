---
description: Register a ColdFusion mapping for a module with the this.cfmapping property in ModuleConfig.
---

# Module CF Mappings

Every module can tell ColdBox what ColdFusion mapping to register for it that points to the module root location on disk when deployed. This is a huge feature for portability and the ability to influence the ColdFusion mappings for you via ColdBox. Just create a `this.cfmapping` property in your `ModuleConfig.bx` (or `.cfc` for CFML):

```javascript
this.cfmapping = "cbstore";
```
