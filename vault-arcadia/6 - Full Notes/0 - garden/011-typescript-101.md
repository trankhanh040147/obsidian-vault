# Simple types
- explicit: writing out the type:
  `let name: string = "kenny"`	
- implicit: TS will guest the type, based on the assigned value:
  `let name = "kenny`
**NOTE:** Having TS guess the type of a value is called **infer**
---
# Unable to infer
TypeScript may not always properly infer what the type of a variable may be. In such cases, it will set the type to `any` which disables type checking.

---
# Types vs Interfaces

