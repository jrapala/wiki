# TypeScript



## What is TypeScript?

- An open-sourced typed syntatic superset of JavaScript developed by Microsoft.
- Compiles to readable JavaScript (the types disappear in the build)
- Comes in three parts: 
  - 1. Language
    2. Language Server (auto-completes, tooltips)
    3. Compiler (used by the server to perform the type-checking, and a way to emit JS files)
       - **Transpilation**: a method where code from one language is "compiled" or converted into another language.
- Works seamlessly with Babel 7 (can use TS files, but does not type-check)
- TypeScript is a development-time technoloy. There is no runtime component.



## Benefits of TypeScript

- Static Typing

- Object-Oriented Programming (OOP) capabilities

  - **Object Oriented Programming**
    - Four Major Principles:
      1. Encapsulation
         - Information hiding. Data is put in a container (usually a `class`). The container protects that data so nothing outside of it can modify or view it. To use the data, you need to use functions that are controlled by the container object.
         - There's no way to hide an object member in JavaScript. TypeScript, however, supports encapsulation with access modifiers such as the `private` method.
      2. Abstraction
         - Related to encapsulation. You hide the internal implementation of how data is managed and provide a more simplified interface to the outside code.
         - Code that is responsible for one set of data should be independent and separated from other code. Ideally, you can change this without adverselt affecting the code in another part.
         - Interface are like classes whose members have no actual working code. It just reveals names and types of object members. 
         - JavaScript does not support interfaces or abstract classes, but TypeScript supports both.
      3. Inheritence
         - Code reuse. Write code once and share it across many others.
         - Both JavaScript and TypeScript support classes and inheritance. 
           - Inheritance is supported via protypical inheritance. Every object instance of a specific type shares the same instance of a single core object (the prototype)
           - In TypeScript, classes can inherit from other classes, but they can also inherit from interfaces and abstract classes. Also allows for multiple inheritance using mixins. 
      4. Polymorphism
         - It is possible to create an oject that can be set to one of any number of possible types that inherit from the same base lineage. Useful in a scenario where the type needed in not immediately knowable but can be set at runtime once the appropriate circumstances have arisen.
         - Technically possible in JavaScript due to its dynamic typing. However with TypeScript, static typing is on by default and forces type declaration when the variable is first created.
    - JavaScript is an OOP language, but the implementation is limited

  

## Types

A **type** is a reusable set of rules. May include properties and functions (capabilities).

When you reuse a type, you are creating an **instance** of it.

### Dynamic Typing vs Static Typing

JavaScript is a dynamically typed language. New variables do not need to declare their type even after they are set. They can be reset to a different type.

TypeScript uses **static typing**. It forces the developer to indicate the type of a variable up front when they create it.



## TypeScript on the Command Line

- `$ tsc file.ts` outputs a `.js` file that can be used by IE6 (ES3)

- `$ tsc file.ts --target ES2015` outputs a cleaner file
- `--module commonjs` is a flag that lets you use commonjs modules (used in Node)
-  `--watch` flag incrementally changes files



## Configuring TypeScript

- A `tsconfig.json` file replaces common line commands. Just run `$ tsc`.

```json
{
	"compilerOptions": {
			"module": "commonjs",
			"target": "es2017",	// Leave as ESNext, or close, and let Babel handle the rest
			"outdir": "lib",	// Location of compiled output. Good for people not using TS.
			"declaration": true,
    	"sourceMap": true,
    	"jsx": "react",		// Lets you parse JSX
    	"strict": true,		// Goal is to keep this on
    	"noImplicitAny": true,	// Leave on to keep type safety
    	"allowJS": true,	// Lets you use jsdoc comments
	},
	"include": ["src"]		// What are we compiling?
}
```



- `declaration: true` provides an `index.d.ts` file (a **type declaration file**)
  - This file has functions with types, but no implementation. 
  - VSCode takes type declaration files and understands what types are needed
-  `sourceMap: true` provides an`index.js.map` file which is a source map (maps debugger to original source)





## TypeScript Basics

### Variables

`let x = "hello world"` Type by **inference** (String). There is not need to type it unless it's for documentation.



`const y = "hello world"` The type is literally "hello world". A **literal type**.



```typescript
const yObj = {
	foo: "hello"
}
```

The type for foo is String because an object is mutable.



`let z: number;` A **type annotation**.



### Arrays

An array of numbers: `let a: number[] = []` 

`let a = [6]` makes an inference that it is an array of numbers.



### Tuples

A tuple is an array of a fixed length.

`let b: [number, string, string, number]`. 

Must be spelled out even if it's all numbers: `let c: [number, number, number, number]`. 

**Note:** `.push` will not warn you of a type mismatch



### Objects

```typescript
let cc: { houseNumber: number; streetName: string };
cc = {
	streetName: "Fake Street",
	houseNumber: 123
};
```

**Optional Type**: `streetName ?: string`



### Intersection and Union Types

#### Intersection Type

You'll only be able to access name in this example (it's the only property that intersects)

```typescript
export interface HasPhoneNumber {
	name: string;
	phone: string;
}

export interface HasEmail {
	name: string;
	email: string;
}

let contactInfo: HasEmail | HasPhoneNumber

contactInfo.name
```



#### Union Types

```typescript
let otherContactInfo: HasEmail & HasPhoneNumber = {
	// we _must_ initialize it to a shape that's asssignable to HasEmail _and_ HasPhoneNumber
	name: "Mike",
	email: "mike@example.com",
	phone: 3215551212
};

otherContactInfo.name; // NOTE: we can access anything on _either_ type
otherContactInfo.email;
otherContactInfo.phone;
```



### Functions

```typescript
// sendEmail has a parameter of type HasEmail and returns an object
function sendEmail(to: HasEmail): { recipient: string; body: string } {
		return {
				recipient: `${to.name} <${to.email}>`, // Mike <mike@example.com>
				body: "You're pre-qualified for a loan!"
		};
}

// OR

const sendEmail = (to: HasPhoneNumber): { recipient: string; body: string } => {
		return {
				recipient: `${to.name} <${to.email}>`, // Mike <mike@example.com>
				body: "You're pre-qualified for a loan!"
    };
};
```



TypeScript can infer return types pretty well, but it's not perfect, expecially with multiple return statements. Type them yourself.



#### Rest Params

```typescript
const sum = (...vals: number[]) => vals.reduce((sum, x) => sum + x, 0);
```



#### Overload Signatures

An **overload signature** is a specific way of a accessing a function.

```typescript
// "overload signatures"
function contactPeople(method: "email", ...people: HasEmail[]): void;
function contactPeople(method: "phone", ...people: HasPhoneNumber[]): void;

// "function implementation"
function contactPeople(
		method: "email" | "phone",
		...people: (HasEmail | HasPhoneNumber)[]
): void {
		if (method === "email") {
				(people as HasEmail[]).forEach(sendEmail);
		} else {
				(people as HasPhoneNumber[]).forEach(sendTextMessage);
		}
}

// ✅ email works
contactPeople("email", { name: "foo", email: "" });

// ✅ phone works
contactPeople("phone", { name: "foo", phone: 12345678 });

// 🚨 mixing does not work
contactPeople("email", { name: "foo", phone: 12345678 });
```



With **scope**:

```typescript
// Note: This function still only have one parameter
function sendMessage(
    this: HasEmail & HasPhoneNumber,
    preferredMethod: "phone" | "email"
) {
  if (preferredMethod === "email") {
      console.log("sendEmail");
      sendEmail(this);
  } else {
      console.log("sendTextMessage");
      sendTextMessage(this);
  }
}
const c = { name: "Mike", phone: 3215551212, email: "mike@example.com" };

function invokeSoon(cb: () => any, timeout: number) {
  	// null is the scope here
    setTimeout(() => cb.call(null), timeout);
}

// 🚨 this is not satisfied
invokeSoon(() => sendMessage("email"), 500);

// ✅ creating a bound function is one solution
const bound = sendMessage.bind(c, "email");
invokeSoon(() => bound(), 500);

// ✅ call/apply works as well
invokeSoon(() => sendMessage.apply(c, ["phone"]), 500);
```



## Type Systems

TypeScript is a **structural type system**. It only cares about the **shape** of an object. Languages like Java are **nominal type systems**. The care if an argument is an instance of a class/type of a specific name (works in OOP)

What is a **shape**? The names of properties and the types of their values.

### Wide vs Narrow Specificity

Wide --> Narrow

`any` -> `any[]` -> `string[]` -> `[string, string, string]` ->`["abc", "abc", string]` -> `never`

.

## Interface vs. Type Alias

### Type Alias

A type alias allows us to give a type a name. They must exist in the space they are being used.

```typescript
type StringOrNumber = string | number;

type HasName = { name: string };
```



Types can be self-referencing:

```typescript
type NumVal = 1 | 2 | 3 | NumVal[];
```



### Interface

The Interface is limited to Javascript objects and its subtypes (arrays and functions).



Interfaces can extend from other interfaces.

```typescript
export interface HasInternationalPhoneNumber extends HasPhoneNumber {
	countryCode: string;
}
```



A interface can be used to describe a function:

```typescript
interface ContactMessenger1 {
	(contact: HasEmail | HasPhoneNumber, message: string): void;
}
```



Here's a type version:

```typescript
type ContactMessenger2 = (
	contact: HasEmail | HasPhoneNumber,
  message: string
) => void;
```



## Contextual Inference

```typescript
// // NOTE: we don't need type annotations for contact or message
const emailer: ContactMessenger1 = (_contact, _message) => {
  /** ... */
};
```



## Contruct Signatures

```typescript
interface ContactConstructor {
 	new (...args: any[]): HasEmail | HasPhoneNumber;
}
```

