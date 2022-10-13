# Prototype Pattern

## What Is It?

A way to share properties among many objects of the same type.

Prevents duplication of methods and properties, thus reducing the amount of memory used.

## Example

```js
class Dog {
  constructor(name) {
    this.name = name;
  }

  bark() {
    return `Woof!`;
  }
}

const dog1 = new Dog("Daisy");
const dog2 = new Dog("Max");
const dog3 = new Dog("Spot");
```

We can see the prototype directly:
```js
// On a constructor:
console.log(Dog.prototype);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()


// On an instance:
console.log(dog1.__proto__);
// constructor: ƒ Dog(name, breed) bark: ƒ bark()
```

**Note:** Due to the **prototype chain**, prototypes themselves can have a `__proto__` object!

### Updating the Prototype
```js
Dog.prototype.play = () => console.log("Playing now!");

dog1.play(); // Playing now!
```

### Extending a Prototype
```js
class SuperDog extends Dog {
  constructor(name) {
    super(name);
  }

  fly() {
    return "Flying!";
  }
}

const dog1 = new SuperDog("Daisy");
dog1.bark(); // Woof!
dog1.fly(); // Flying!
```

### Prototype Pattern Using Objects

```js
const dog = {
  bark() {
    return `Woof!`;
  }
};

const pet1 = Object.create(dog);

pet1.bark(); // Woof!

console.log("Direct properties on pet1: ", Object.keys(pet1));
// []

console.log("Properties on pet1's prototype: ", Object.keys(pet1.__proto__));
// ["bark"]

```

`Object.create` lets objects inherit properties from other objects, by specifying the newly created object's prototype.

.