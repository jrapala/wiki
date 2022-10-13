# Mixin Pattern

## What Is It?

A object that we can use in order to add reusable functionality to another object or class, without using inheritance. Cannot be used on their own.

Modifying an object's prototype is seen as bad practice, as it can lead to prototype pollution and a level of uncertainty regarding the origin of our functions.

## Example

### Simple
```js
class Dog {
  constructor(name) {
    this.name = name;
  }
}

const dogFunctionality = {
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!")
};

// Add properties to target object
Object.assign(Dog.prototype, dogFunctionality);

const pet1 = new Dog("Daisy");

pet1.name; // Daisy
pet1.bark(); // Woof!
pet1.play(); // Playing!
```

### Mixins Using Inheritence
```js
class Dog {
  constructor(name) {
    this.name = name;
  }
}

const animalFunctionality = {
  walk: () => console.log("Walking!"),
  sleep: () => console.log("Sleeping!")
};

const dogFunctionality = {
  __proto__: animalFunctionality,
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!"),
  walk() {
    super.walk();
  },
  sleep() {
    super.sleep();
  }
};

// Add properties to target object
Object.assign(Dog.prototype, dogFunctionality);
```

### Real World Example
The `Window` object implements many of its properties from the [`WindowOrWorkerGlobalScope`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope) and [`WindowEventHandlers`](https://developer.mozilla.org/en-US/docs/Web/API/WindowEventHandlers) mixins, which allow us to have access to properties such as `setTimeout` and `setInterval`, `indexedDB`, and `isSecureContext`.

```js
window.indexedDB.open("toDoList");

window.addEventListener("beforeunload", event => {
  event.preventDefault();
  event.returnValue = "";
});

window.onbeforeunload = function() {
  console.log("Unloading!");
};

console.log(
  "From WindowEventHandlers mixin: onbeforeunload", 
  window.onbeforeunload
);
// f () {}

console.log(
  "From WindowOrWorkerGlobalScope mixin: isSecureContext",
  window.isSecureContext
);
// true

console.log(
  "WindowEventHandlers itself is undefined",
  window.WindowEventHandlers
);
// undefined

console.log(
  "WindowOrWorkerGlobalScope itself is undefined",
  window.WindowOrWorkerGlobalScope
);
// undefined
```

## Cons
[Discouraged by the React team](https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html). Use[ HOC's](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750) instead.