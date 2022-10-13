# Module Pattern

## What Is It?

It allows you to split up your code into smaller, reusable pieces.

It keeps certain value within your file private. Declarations within a module are scoped (encapsulated) to that module, by default. You'll need to explicitly export values. This reduces the risk of name collisions.


## Example

### ES2015 Modules (Babel required to work in all runtimes)
```js
// math.js
export function add(x, y) {
  return x + y;
}
```

```js
// index.js
import { add } from "./math.js";
```

### Renaming Imported Values
```js
import {
  add as addValues,
} from "./math.js";

function add(...args) {
  return args.reduce((acc, cur) => cur + acc);
}

addValues(7, 8);
add(8, 9, 2, 10);
```

### Default Exports
```js
// math.js
export default function add(x, y) {
  return x + y;
}

export function multiply(x) {
  return x * 2;
}
```

```js
// index.js
import add, { multiply } from "./math.js";
```

### Importing All Exports
```js
import * as math from "./math.js";

math.default(7, 8);
math.multiply(8, 9);
```

Private values in `math.js` are still private until they are explicitly exported.

### Dynamic Imports
When importing all modules on the top of a file, all modules get loaded before the rest of the file. In some cases, we only need to import a module based on a certain condition. With a **dynamic import**, we can import modules on demand:

```js
const button = document.getElementById("btn");

button.addEventListener("click", () => {
  import("./math.js").then((module) => {
    console.log("Add: ", module.add(1, 2));
    console.log("Multiply: ", module.multiply(3, 2));
  
    const button = document.getElementById("btn");
    button.innerHTML = "Check the console";
  });
});

  
/*************************** */
/**** Or with async/await ****/
/*************************** */

// button.addEventListener("click", async () => {
//   const module = await import("./math.js");
//   console.log("Add: ", module.add(1, 2));
//   console.log("Multiply: ", module.multiply(3, 2));
// });
```

We don't even need to use hardcoded module paths:
```js
const res = await import(`../assets/dog${num}.png`);
```
