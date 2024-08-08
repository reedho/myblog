+++
title = "TypeScript Name Space Tricks"
date = 2024-08-08
+++

One interesting trick from @mattpocockuk in the way how `namespace` works in
TypeScript.

When we export a namespace with the same name with a function, the declaration
will be merged.

```typescript
export declare namespace myFunc {
  type MyType = string
}

export function myFunc(n: number): string {
  return `${n}`
}
```

Then, in other file we can:

```typescript
import { myFunc } from './lib/myFunc'

let x: myFunc.MyType = myFunc(1)
console.log(x)
```
