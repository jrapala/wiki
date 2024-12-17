# TypeScript's Type System

## Item 6: Use Your Editor to Interrogate and Explore the Type System

When you install TypeScript, you get two executables:
- `tsc`: the TypeScript compiler
- `tsserver`: the TypeScript standalone server (provides language services like autocomplete, inspection, navigation, and refactoring)

You can:
- Mouse over a symbol to see what TypeScript considers its type, including inferred return values
	- If an inferred value does not match your expectation, be sure to add a type declaration
	- These inferred values can narrow within conditionals
- "Go to Definition" helps you navigate libraries and type declarations

## Item 7: Think of Types as Sets of Values

Variables have types, which is best thought of as a set of values, which is known at the domain of the type.

The smallest set is the empty set, which is type **never**. No values are assignable to a variable with a never type (it has an empty domain).

The next smallest sets are those that contain single values (i.e. unit types).

You can union unit types together into the next smallest set.

When you see `Type '"C"' is not assignable to type 'AB'` it means that one value is not a subset of another.

An intersection of types (&) applies to the sets of values (the domain of the type), and not to the properties in the interface. A value that has the properties of *both* `Person` and `Lifespan` will belong to the intersection type:

```ts
interface Person {
  name: string;
}

interface Lifespan {
  birth: Date;
  death?: Date;
}

type PersonSpan = Person & Lifespan;

const ps: PersonSpan = {
  name: 'Alan Turing',
  birth: new Date('1912/06/23'),
  death: new Date('1954/06/07')
}

type K = keyof (Person | Lifespan); // Type is never
```

A more common way to write this:
```ts
interface Person {
  name: string;
}

// PersonSpan is a subset of Person
interface PersonSpan extends Person {
  birth: Date;
  death?: Date;
}
```
