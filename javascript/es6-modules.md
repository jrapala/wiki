# ES6 Modules

A JavaScript *module* is a piece of reusable code that can easily be incorporated into other JavaScript files without causing variable collisions. JavaScript modules are stored in separate files, one file per module. There are two options when creating and exporting a module: you can export multiple JavaScript objects from a single module or one JavaScript object per module.

In *text-helpers.js*, two functions are exported:

```javascript
export const print=(message) => log(message, new Date())

export const log=(message, timestamp) =>
  console.log(`${timestamp.toString()}: ${message}`)
```

`export` can be used to export any JavaScript type that will be consumed in another module. In this example, the `print` function and `log` function are being exported. Any other variables declared in *text-helpers.js* will be local to that module.

Modules can also export a single main variable. In these cases, you can use `export default`. For example, the *mt-freel.js* file can export a specific expedition:

```javascript
export default new Expedition("Mt. Freel", 2, ["water", "snack"]);
```

`export default` can be used in place of `export` when you wish to export only one type. Again, both `export` and `export default` can be used on any JavaScript type: primitives, objects, arrays, and functions.

Modules can be consumed in other JavaScript files using the `import` statement. Modules with multiple exports can take advantage of object destructuring. Modules that use `export default` are imported into a single variable:

```javascript
import { print, log } from "./text-helpers";
import freel from "./mt-freel";

print("printing a message");
log("logging a message");

freel.print();
```

You can scope module variables locally under different variable names:

```javascript
import { print as p, log as l } from "./text-helpers";

p("printing a message");
l("logging a message");
```

You can also import everything into a single variable using `*`:

```javascript
import * as fns from './text-helpers`
```

This `import` and `export` syntax is not yet fully supported by all browsers or by Node. However, like any emerging JavaScript syntax, it’s supported by Babel. This means you can use these statements in your source code and Babel will know where to find the modules you want to include in your compiled JavaScript.



## CommonJS

CommonJS is the module pattern that’s supported by all versions of Node (see the [Node.js documentation on modules](https://oreil.ly/CN-gA)). You can still use these modules with Babel and webpack. With CommonJS, JavaScript objects are exported using `module.exports`.

For example, in CommonJS, we can export the `print` and `log` functions as an object:

```javascript
const print(message) => log(message, new Date())

const log(message, timestamp) =>
console.log(`${timestamp.toString()}: ${message}`}

module.exports = {print, log}
```

CommonJS does not support an `import` statement. Instead, modules are imported with the `require` function:

```javascript
const { log, print } = require("./txt-helpers");
```

