# Generics

What are generics? It's three different things:
1. Passing types to types
```ts
type PartialUser = Partial<{ id: string; name: string }>;
```
2. Passing types to functions
```ts
const stringSet = new Set<string>();
```
3. Inferring types from arguments passed to functions
```ts
const objKeys = <T extends object>(obj: T) => {
	return Object.keys(obj) as Array<keyof T>;
};

const keys = objKeys({ a: 1, b: 2 });
```

## Passing Types to Types

You can declare a type:

```ts
// Represents an object
export type User = {
	id: string;
	name: string;
}

// Represents a function
export type ToString = (input: any) => string;
```

Sometimes you need to create types with similar structures:

```ts
type ErrorShape = {
	message: string;
	code: number;
}

type GetUserData = {
	data: {
		id: string;
		name: string;
	};
	error?: ErrorShape;
}

type GetPostsData = {
	data: {
		id: string;
		title: string;
	}[];
	error?: ErrorShape;
}

type GetCommentsData = {
	data: {
		id: string;
		content: string;
	}[];
	error?: ErrorShape;
}
```

An OOP-orientated refactor:

```ts
type ErrorShape = {
	message: string;
	code: number;
}

interface DataBaseInterface {
	error?: ErrorShape;
}

interface GetUserData extends DataBaseInterface {
	data: {
		id: string;
		name: string;
	};
}

interface GetPostsData extends DataBaseInterface {
	data: {
		id: string;
		title: string;
	}[];
}

interface GetCommentsData extends DataBaseInterface {
	data: {
		id: string;
		content: string;
	}[];
}
```

But it's more concise to create a **type function**:

```ts
type ErrorShape = {
	message: string;
	code: number;
}

type DataShape<TData> = {
	data: TData;
	error?: ErrorShape;
}

type GetUserData = DataShape<{
	id: string;
	name: string;
}>;

type GetPostsData = DataShape<
	{
		id: string;
		title: string;
	}[]
>;

type GetCommentsData = DataShape<
	{
		id: string;
		content: string;
	}[]
>;
```

The angle brackets tell TypeScript that we want to add a type argument to this type. You can name it anything. This is a **generic type**:

```ts
// Generic Type
type DataShape<TData> = {
	data: TData;
	error?: ErrorShape;
}

// Passing our generic type another type
type GetPostsData = DataShape<
	{
		id: string;
		title: string;
	}[]
>;
```


## Passing Types to Functions

Generic types can accept multiple type arguments. 
You can provide defaults to type arguments.
You can provide constraints on type arguements.
And it's not just types that you can pass types to. Functions can accept types as arguments.

```ts
const createSet = <T>() => {
	return new Set<T>();
}

// A set that only accepts strings
const stringSet = createSet<string>();

// A set that only accepts numbers
const numberSet = createSet<number>();

// Argument of type 'number' is not assignable
// to parameter of type 'string'
stringSet.add(123)
```

React does this as well:

```ts
const [index, setIndex] = useState<number>(0);
```

But the number type can be inferred:

```ts
const [index, setIndex] = useState(0)
// const index: number
```

Why? Because we can type the initial value.
Let's revise the previous Set functions:

```ts
const createSet = <T>(initial: T) => {
	return new Set<T>([initial])
}

// TypeScript can infer the type <T> from the runtime argument
const stringSet = createSet("matt")
```