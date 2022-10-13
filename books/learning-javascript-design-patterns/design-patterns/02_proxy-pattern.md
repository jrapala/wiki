# Proxy Pattern

## What Is It?

Instead of interacting directly with an object, you interact with a proxy. 
They add control over the behavior of an object. 
A proxy can have various use-cases: it can help with validation, formatting, notifications, or debugging.

## Example

### Basic

```js
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American"
};

const personProxy = new Proxy(person, {});
```

The second argument (`{}`) is the **handler**, where we can define specific behaviors based on the type of interaction.

### With Handler

```js
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American"
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    obj[prop] = value;
  }
});


personProxy.name; // The value of name is John Doe
personProxy.age = 43; // Changed age from 42 to 43

```

### With Validation

```js
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    if (!obj[prop]) {
      console.log(
        `Hmm.. this property doesn't seem to exist on the target object`
      );
    } else {
      console.log(`The value of ${prop} is ${obj[prop]}`);
    }
  },
  set: (obj, prop, value) => {
    if (prop === "age" && typeof value !== "number") {
      console.log(`Sorry, you can only pass numeric values for age.`);
    } else if (prop === "name" && value.length < 2) {
      console.log(`You need to provide a valid name.`);
    } else {
      console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`);
      obj[prop] = value;
    }
  }
});

personProxy.nonExistentProperty; // Hmm.. this property doesn't seem to exist
personProxy.age = "44"; // Sorry, you can only pass numeric values for age.
personProxy.name = ""; // You need to provide a valid name.

```


### With Reflect (build-in object)
```js
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American"
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${Reflect.get(obj, prop)}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    return Reflect.set(obj, prop, value);
  }
});

personProxy.name; // The value of name is John Doe
personProxy.age = 43; // Changed age from 42 to 43
personProxy.name = "Jane Doe"; // Changed name from John Doe to Jane Doe
```

Instead of accessing properties through `obj[prop]` or setting properties through `obj[prop] = value`, we can access or modify properties on the target object through `Reflect.get()` and `Reflect.set()`. The methods receive the same arguments as the methods on the handler object.

## Cons

Overusing the `Proxy` object or performing heavy operations on each `handler` method invocation can easily affect the performance of your application negatively. It's best to not use proxies for performance-critical code.