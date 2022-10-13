# Singleton Pattern

## What Is It?

A class that instantiated once, and can be accessed globally. 
Great for managing global state.
In React, Redux of React Context is better, as they provide a read-only state (singletons are mutable).

## Example

### Overkill Method (Using a Class)

```js
let counter = 0;

class Counter {
  constructor() {
    if (instance) {
      throw ne Error("You can only create one instance!")
    }
    instance = this;
  }

  getInstance() {
    return this;
  }

  getCount() {
    return counter;
  }

  increment() {
    return ++counter;
  }

  decrement() {
    return --counter;
  }
}

const singletonCounter = Object.freeze(new Counter());

export default singletonCounter;
```

### Object Method
```js
let count = 0;

const counter = {
  increment() {
    return ++count;
  },

  decrement() {
    return --count;
  }
};

Object.freeze(counter);

export { counter };
```

## Cons
- Testing can get tricky - can't create new instances each time.
- When you import a singleton, it's not clear that it's a singleton. Using it may change other parts of the app.