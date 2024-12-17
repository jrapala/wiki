# Getting to Know TypeScript

## Item 1: Understand the Relationship Between TypeScript and JavaScript

TypeScript is a superset of JavaScript. It is not a separate language. TypeScript parses code and emits JavaScript.

All JavaScript programs are TypeScript but not all TypeScript programs are JavaScript programs (due to the additional syntax).

One of the goals of TypeScript is to detect code that will throw an exception at runtime, without having to run your code. This is known as a "static" type system.

TypeScript *models* the runtime behavior of JavaScript. It will sometimes flag issues even if they will not throw exceptions at runtime. So it is a bit opinionated by the creators of TypeScript.

A type system which can guarantee the accuracy of its static types is said to be *sound*. TypeScript is not sound. Try Reason or Elm instead.

## Item 2: Know Which TypeScript Options You’re Using

Create a configuration file (`tsconfig.json`) with `tsc --init`. This lets you set what TypeScript options you'd like to use.

The most important are:
- `noImplicitAny`: controls whether variables must have known types
	- TypeScript is most helpful when it has type information
- `strictNullChecks`: controls whether null and undefined are permissible in EVERY type
	- Very helpful for catching errors involving null and undefined values (e.g. `undefined is not an object`)

Your goal is to enable `strict` so TypeScript can catch most errors.

## Item 3: Understand That Code Generation Is  Independent of Types

`tsc` is the TypeScript compiler.
- It converts ("transpiles") TypeScript/JavaScript to an old version that works with most browsers.
- It checks your code for type errors.

### Code with Type Errors Can Produce Output

Code with type errors can produce output (it doesn't stop the build).
- Therefore, if your TypeScript code has errors, instead of saying your code "doesn't compile", say it "doesn't type check".

You can disable output on errors with `noEmitOnError`

### You Cannot Check TypeScript Types at Runtime

You cannot type check at runtime. e.g. `if (shape instanceOf Rectangle)`. Types are erased at runtime. Instead, you'll need to reconstruct the type in some way, during runtime.

Some ways to do this:

```ts
function calculateArea(shape: Shape) {
	// A property check
	if ('height' in shape) {
		...
	}
}
```

```ts
interface Square {
	kind: "square";
}

interface Rectangle {
	kind: "rectangle";
}

// A 'tagged' union
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
	if (shape.kind === 'rectangle') {
		...
	}
}
```

```ts
// A class constructor introduces both a type and a value
class Square {
	constructor(public width: number) {}
}

class Rectangle extends Square {
	constructor(public width: number, public height: number) {
		super(width);
	}
}

// Refers to *type*
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
    // Refers to *value*
	if (shape instanceof Rectangle) {
		...
	}
}
```

### Type Operations Cannot Affect Runtime Values

Normalizing items this way does not work:

```ts
function asNumber(val: number | string) {
  return val as number;
}
```

You need to check runtime type instead:

```ts
function asNumber(val: number | string) {
  return typeof(val) === "string" ? Number(val) : val;
}
```

### Runtime Types May Not Be the Same as Declared Types

It's always possible for a runtime type to be different than a declared type.

For example, if you are using an API response for a function, if you misunderstood the API, then the runtime types may not be the same as the declared types.

#### You Cannot Overload a Function Based on TypeScript Types

**Function overloading** is defining multiple versions of a function that differ only in the types of their parameters. This is not possible in TypeScript.

You can, however, provide multiple declarations for a function, but you can only have a single implementation.

```ts
function add(a: number, b: number): number;
function add(a: string, b: string): string;

function add(a, b) {
  return a + b;
}

```

### TypeScript Types Have No Effect on Runtime Performance

Since they get erased, static types are truly zero cost. There is no runtime overhead.

However there is build time overhead, and occasionally a performance overhead as well.
- If build time is an issue, try a "transpile only" option to skip type checking.

## Item 4: Get Comfortable with Structural Typing

%% Revisit this section %%

JavaScript is inherently duck typed--it doesn't care how the values going into functions were created.
TypeScript uses structural typing to model this.
If a structure is compatible, the function will not emits errors.

For example:

```ts
interface Vector2D {
  x: number;
  y: number;
}

function calculateLegth(v: Vector2D) {
  return Math.sqrt(v.x * v.x + v.y * v.y);
}

interface NamedVector {
  name: string;
  x: number;
  y: snumber;
}

const v: NamedVector = { x: 3, y: 4, name: 'Zee'};
calculateLength(v); // This is okay
```

This can cause problems if you pass in a new 3D vector into `calculateLength`. You will not get your expected value and TypeScript will not warn you about it.

Types are not "sealed" or "precise." They are "open." Values assignable to your interfaces might have properties beyond those explicitly listed in your type declarations.

Structural typing is helpful when facilitating unit tests. You do not need to mock libraries if you introduce abstractions that free logic from details of a specific implementation.

## Item 5: Limit Use of the any Type

TypeScript's type system is gradual (you can add types bit by bit) and optional (you can disable the type checker whenever you like) with the use of **any**.

### There's No Type Safety with any Types

Any error will go uncaught.

### any lets you break contracts

```ts
function calculateAge(birthdate: Date): number {
  // ...
}

let birthdate: any = '1990-01-19'; // break contract
calculateAge(birthDate); // OK
```

### There Are No Language Services for any Types

No autocompletion, no "Rename Symbol" option

### any Types Mask Bugs When You Refactor Code

### any Hides Your Type Design

### any Undermines Confidence in the Type System

Having lots of any types can be harder to work with than untyped JavaScript because you have to fix type errors *and* keep track of the real types in your head.
``