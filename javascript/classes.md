# Classes

JavaScript uses something called `prototypical inheritance`. This technique can be wielded to create structures that feel object-oriented. For example, we can create a `Vacation` constructor that needs to be invoked with a `new` operator:

```javascript
function Vacation(destination, length) {
  this.destination = destination;
  this.length = length;
}

Vacation.prototype.print = function() {
  console.log(this.destination + " | " + this.length + " days");
};

const maui = new Vacation("Maui", 7);

maui.print(); // Maui | 7 days
```

This code creates something that feels like a custom type in an object-oriented language. A `Vacation` has properties (destination, length), and it has a method (print). The `maui` instance inherits the `print` method through the prototype. 



Functions are objects, and inheritance is handled through the prototype. Classes provide a syntactic sugar on top of that gnarly prototype syntax:

```javascript
class Vacation {
  constructor(destination, length) {
    this.destination = destination;
    this.length = length;
  }

  print() {
    console.log(`${this.destination} will take ${this.length} days.`);
  }
}
```

When you’re creating a class, the class name is typically capitalized. Once you’ve created the class, you can create a new instance of the class using the `new` keyword. Then you can call the custom method on the class:

```javascript
const trip = new Vacation("Santiago, Chile", 7);

trip.print(); // Chile will take 7 days.
```



Classes can also be extended. When a class is extended, the subclass inherits the properties and methods of the superclass. These properties and methods can be manipulated from here, but as a default, all will be inherited.

You can use `Vacation` as an abstract class to create different types of vacations. For instance, an `Expedition` can extend the `Vacation` class to include gear:

```javascript
class Expedition extends Vacation {
  constructor(destination, length, gear) {
    super(destination, length);
    this.gear = gear;
  }

  print() {
    super.print();
    console.log(`Bring your ${this.gear.join(" and your ")}`);
  }
}
```

That’s simple inheritance: the subclass inherits the properties of the superclass. By calling the `print` method of `Vacation`, we can append some new content onto what is printed in the `print` method of `Expedition`. Creating a new instance works the exact same way—create a variable and use the `new` keyword:

```javascript
const trip = new Expedition("Mt. Whitney", 3, [
  "sunglasses",
  "prayer flags",
  "camera"
]);

trip.print();

// Mt. Whitney will take 3 days.
// Bring your sunglasses and your prayer flags and your camera
```