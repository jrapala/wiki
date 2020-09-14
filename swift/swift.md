# Swift

[TOC]

## Basics

### Variables

`var str = "Hello, playground"`



### Data Types

Swift is a type-safe language.

Types:

- `String` (use double quotes)
- `Int`
- `Double` (any fractional number) (there is also a `Float`, but `Double` is twice as accurate)
- `Bool`



A `Double` and an `Int` take up the same amount of memory.



A large int can use underscores as separators (helps you read it)

`var population = 8_000_000`



### Multi-Line Strings

Use backslashes to remove line breaks.

```swift
var str1 = """
This goes
over multiple
lines
"""

// "This goes\nover multiple\nlines"

var str2 = """
This goes \
over multiple \
lines
"""

// "This goes over multiple lines"
```



### String Interpolation

```swift
var score = 85
var str = "Your score was \(score)"
```



### Constants

Unlike JavaScript, the `let` keyword is used for constants. Use it whenever you can.

`let taylor = "swift"`



### Type Annotations

Swift can infer types, but to be explicit:

```swift
let album: String = "Reputation"
let year: Int = 1989
let height: Double = 1.78
let taylorRocks: Bool = true
let greetings: [String] = ["hello"]
let widths: [String: Int] = ["elephant": 100]
```



## Complex Data Types

### Arrays

```swift
let john = "John Lennon"
let paul = "Paul McCartney"
let george = "George Harrison"
let ringo = "Ringo Starr"

let beatles = [john, paul, george, ringo]
beatles[1]
```

**Note:** Swift will crash if it tries to read an item that does not exist.



### Sets

Items are stored in a random order (this optimizes them for fast retrieval.)

All items must be unique.

You must pass in an array.

```swift
let colors = Set(["red", "green", "red"])

// {"green", "red"}
```



### Tuples

Similar to an array, except:

1. You can’t add or remove items from a tuple; they are fixed in size.
2. You can’t change the type of items in a tuple; they always have the same types they were created with.



```swift
var name = (first: "Taylor", last: "Swift")

name.0
// "Taylor" 

name.first
// "Taylor"
```

**Note:** You cannot read numbers or names that do not exist



### Dictionaries

Array of key/value pairs.

```swift
let heights = [
    "Taylor Swift": 1.78,
    "Ed Sheeran": 1.73
]

heights["Taylor Swift"]
```



To add a default value:

```swift
let favoriteIceCreams = [
    "Paul": "Chocolate",
    "Sophie": "Vanilla"
]

favoriteIceCreams["Paul"]
favoriteIceCreams["Charlotte"]		// nil
favoriteIceCreams["Charlotte", default: "Unknown"]		// "Unknown"
```



### Collections

Arrays, Sets, and Dictionaries are all know as **Collections**

To create an empty collection:

```swift
// An empty Dictionary
var teams = [String: String]()
teams["Paul"] = "Red"

// An empty Array
var results = [Int]()

// An empty Set (you need to specify "Set")
var words = Set<String>()
var numbers = Set<Int>()

// The same style can be used for Dictionaries and Arrays, if desired
var scores = Dictionary<String, Int>()
var results = Array<Int>()
```



### Eumerations

A way of defining groups of values (prevents spelling mistakes)

```swift
enum Result {
  case success
  case failure
}

let result = Result.failure
```



### Enum associated values

A way Swift allows you to attach more information to your enums. 

```swift
enum Activity {
	case bored
	case running(destination: String)
	case talking(topic: String)
  case singing(volume: Int)
}

let talking = Activity.talking(topic: football)
```



Why do this?

```swift
enum Weather {
    case sunny
    case windy(speed: Int)
    case rainy(chance: Int, amount: Int)
}

// is better than:

enum Weather {
    case sunny
    case lightBreeze
    case aBitWindy
    case quiteBlusteryNow
    case nowThatsAStrongWind
    case thisIsSeriousNow
    case itsAHurricane
}
```



### Enum raw values

Lets you give meaning to enum cases. Lets you create enums from integers or strings.

```swift
enum Planet {
  case mercury
  case venus
  case earth
  case mars
}

// What if you forget what the 3rd planet is?
// Or, what if you just want a simple numeric server response?
let earth = Planet(rawValue: 2)
```



If you don't like the automatic numbering:

```swift
// set one, the rest are automatically set
enum Planet {
  case mercury = 1
  case venus
  case earth
  case mars
}

let earth = Planet(rawValue: 3)
```

[In-Depth Reading](https://www.avanderlee.com/swift/enumerations/)



## Operators and Conditions

### Arithmetic Operators

```swift
let firstScore = 12
let secondScore = 4

let total = firstScore + secondScore
let difference = firstScore - secondScore
let product = firstScore * secondScore
let divided = firstScore / secondScore

let remainder = 13 % secondScore		// 1
```

Operating with an `Double` and an `Int` creates a `Double`

**Tip:** You can use `varName.isMultiple(of: 7)` to check if a number is a multiple of another.



### Operator overloading

What an operator does depends on the values you use it with.

Swift is type-safe so you cannot add mixed types (no coercion)

```swift
// Add ints together
let meaningOfLife = 42
let doubleMeaning = 42 + 42

// Add strings together
let fakers = "Fakers gonna "
let action = fakers + "fake"

// Add arrays together
let firstHalf = ["John", "Paul"]
let secondHalf = ["George", "Ringo"]
let beatles = firstHalf + secondHalf
```



### Compound assignment operators

Shorthand operators to change variables in place.

```swift
var score = 95
score -= 5

var quote = "The rain in Spain falls mainly on the "
quote += "Spaniards"
```

Remember, this only works on `var`

### Comparison operators

```swift
let firstScore = 6
let secondScore = 4

firstScore == secondScore // false
firstScore != secondScore // true

firstScore < secondScore // false
firstScore >= secondScore // true

// Compares alphabetical order
"Taylor" <= "Swift" 
```

You cannot compare an `Int` to a `Double`, not `String` to a number. It is invalid. 



Comparison operators work for enums as well:

```swift
enum Sizes: Comparable {
    case small
    case medium
    case large
}

let first = Sizes.small
let second = Sizes.large
print(first < second) // True
```



### Conditions

`if` / `else if` / `else`

```swift
let firstCard = 11
let secondCard = 10

if firstCard + secondCard = 2 {
  print("Aces! Lucky!")
} else if firstCard + secondCard == 21 {
    print("Blackjack!")
} else {
  print("Regular cards")
}
```



### Combining conditions

`&&` and `||`

```swift
let age1 = 12
let age2 = 21

if age1 > 18 && age2 > 18 {
    print("Both are over 18")
}

if age1 > 18 || age2 > 18 {
    print("At least one is over 18")
}
```



### The ternary operator

```swift
let firstCard = 11
let secondCard = 10
print(firstCard == secondCard ? "Cards are the same" : "Cards are different")
```



### Switch statements

```swift
let weather = "sunny"

switch weather {
  case "rain":
      print("Bring an umbrella")
  case "snow":
      print("Wrap up warm")
  case "sunny":
      print("Wear sunscreen")
  default:
      print("Enjoy your day!")
}
```

The `default` is necessary if your `switch` statement is not exhaustive (e.g. does not check every value in an enum)



You can add a `fallthrough` keyword if you want to continue execution:

```swift
case "sunny":
    print("Wear sunscreen")
    fallthrough
default:
    print("Enjoy your day!")
}
```



### Range operators

Two ways to create ranges:

1. The half-open range operator`..<` creates ranges up to but *excluding* the final value.
2. The closed ranger operator `...` creates ranges up to and *including* the final value.

```swift
let score = 85

switch score {
  case 0..<50:
      print("You failed badly.")
  case 50..<85:
      print("You did OK.")
  default:
      print("You did great!")
}

// "You did great!"
```



## Loops

### For loops

```swift
let count 1...10

for number in count {
  print("Number is \(number)")
}

for album in albums {
  print("\(album) is on Apple Music")
}

// for loops create a constant
// If you don't need it, you can use a _ to prevent 
// a constant from being created.
for _ in 1...5 {
  print("play")
}
```



### While loops

```swift
var number = 1

while number <= 20 {
  print(number)
  number += 1
}

print("Ready or not, here I come!")
```



### Repeat loops

Not commonly used. Will always be executed at least once.

```swift
var number = 1

repeat {
    print(number)
    number += 1
} while number <= 20

print("Ready or not, here I come!")

```



One use case:

```swift
let numbers = [1, 2, 3, 4, 5]
var random: [Int]

repeat {
    random = numbers.shuffled()
} while random == numbers
```



### Exiting loops

Use the `break` keyword.

```swift
while countDown >= 0 {
    print(countDown)

    if countDown == 4 {
        print("I'm bored. Let's go now!")
        break
    }

    countDown -= 1
}
```



### Labeled Statements

A `break` will only exit an inner loop. To exit a nested loop, add a `labeled statement` to the outer loop and call it after `break`.

```swift
// Add the label
outerLoop: for i in 1...10 {
    for j in 1...10 {
        let product = i * j
        print ("\(i) * \(j) is \(product)")

        if product == 50 {
            print("It's a bullseye!")
	          // Call it. Both loops are exited.
            break outerLoop
        }
    }
}
```



### Skipping items

If you just want to skip the current item and continue on to the next one, you should use `continue` instead.

```swift
for i in 1...10 {
    if i % 2 == 1 {
        continue
    }

    print(i)
}
```



### Infinite loops

You may find the need to create **pseudo-infinite** loops (loops that are infinite, except for an exit case)

```swift
var counter = 0

while true {
    print(" ")
    counter += 1

    if counter == 273 {
        break
    }
}
```



## Functions

### Basic

```swift
func printHello() {
	let message = "Hello World"
	
	print(message)
}

printHello()
```



### With Parameters

```swift
func square(number: Int) {
	print(number * number)
}

// Use the parameter name when running the function
square(number: 8)


// Option to not use a parameter name
func greet(_ person: String) {
    print("Hello, \(person)!")
}

greet("Taylor")
```



You can use two names for the parameter. One to be used externally, and one to be used internally:

```swift
// "to" is external
// "name" is internal
func sayHello(to name: String) {
  print("Hello, \(name)!")
}

sayHello(to: "Taylor")
```

```swift
func setAge(for person: String, to value: Int) {
    print("\(person) is now \(value)")
}

// Allows you to write plain English
// You wouldn't even be abe to use "for" inside the function
setAge(for: "Paul", to: 40)
```



Parameter labels are used extensively in Swift.



### Default Parameters

```swift
func greet(_ person: String, nicely: Bool = true) {
    if nicely == true {
        print("Hello, \(person)!")
    } else {
        print("Oh no, it's \(person) again...")
    }
}

greet("Taylor")
greet("Taylor", nicely: false)
```



An example of a function using a default parameter is the `print()` function. It contains a `terminator` parameter that lets you replace the default new line that is added at the end of the string.

```swift
print("I'm a sentence.", terminator: "")
```





### Returning Values

```swift
func square(number: Int) -> Int {
    return number * number
}
```



You can skip the return keyword if you only have one expression in the function:

```swift
func doMoreMath() -> Int {
    5 + 5
}

func greet(name: String) -> String {
    name == "Taylor Swift" ? "Oh wow!" : "Hello, \(name)"
}
```



**Tip:** If you return multiple values, that is a perfect place to use tuples

```swift
func getUser() -> (first: String, last: String) {
    (first: "Taylor", last: "Swift")
}

let user = getUser()
print(user.first)
```

This is ideal because:

1. Our data must be returned first name first and last name second.
2. If we insert a middle name it will not affect the position of other values.
3. We can’t make case-sensitive mistakes while reading values.
4. There's no way we won’t get back a first name or last name – if someone only uses one name then we’ll get back an empty string.
5. There is no optionality.



### Variadic Functions

These functions accept any number of parameters of the same type (including zero parameters). The `print()` function is one of them. This works: `print("Haters", "gonna", "hate")`. The parameters are turned into an array.



To create a variadic function:

```swift
func square(numbers: Int...) {
    for number in numbers {
        print("\(number) squared is \(number * number)")
    }
}

square(numbers: 1, 2, 3, 4, 5)
```



### Throwing Functions

```swift
enum PasswordError: Error {
    case obvious
}

// Add throws before return type
func checkPassword(_ password: String) throws -> Bool {
    if password == "password" {
      	// throw the error
        throw PasswordError.obvious
    }

    return true
}

// You need a do, a try, and a catch to call a throwing function
do {
    try checkPassword("password")
    print("That password is good!")
} catch {
    print("You can't use that password.")
}

// "You can't use that password."

```



### Inout Parameters

A `inout` can be used to change variables inside a function.

```swift
func doubleInPlace(number: inout Int) {
    number *= 2
}

// You must use a variable
var myNum = 10 

// You must add an & to specify it's being used as an inout
doubleInPlace(number: &myNum)
```



## Closures

[Helpful Reference Site - goshdarnclosuresyntax.com](http://goshdarnclosuresyntax.com/)

### Basic Closure

Here we create a function without a name. That function is assigned to `driving`. `driving` can now be called as a regular function. This type of function is called a `closure`.

```swift
let driving = {
    print("I'm driving in my car")
}

driving()
```



### Adding Parameters

To add parameters, you can add them inside the open braces. Then add `in` to tell Swift the body of the closure is starting:

```swift
let driving = { (place: String) in
    print("I'm going to \(place) in my car")
}
```

Note: closures cannot use external parameter labels.



You do not use parameter labels when calling the function:

```swift
driving("London")
```



### Adding Return Values

Add directly before the `in` keyword:

```swift
let drivingWithReturn = { (place: String) -> String in
    return "I'm going to \(place) in my car"
}

let message = drivingWithReturn("London")
print(message)
```



### Passing Closures Into Other Functions

```swift
// action is a function that returns nothing
func travel(action: () -> Void) {
    print("I'm getting ready to go.")
    action()
    print("I arrived!")
}

travel(action: driving)
```



If the last parameter to a function is a closure, Swift lets you use **trailing closure syntax**. Rather than passing in your closure as a parameter, you can pass it directly after the function braces:

```swift
func travel(action: () -> Void) {
    print("I'm getting ready to go.")
    action()
    print("I arrived!")
}

// Travel can be called like this:
travel() {
    print("I'm driving in my car")
}

// Or like this, since no other parameters

travel {
  print("I'm driving in my car")
}
```

**This is extremely common in Swift.**



Another example:

```swift
func animate(duration: Double, animations: () -> Void) {
    print("Starting a \(duration) second animation…")
    animations()
}

animate(duration: 3, animations: {
    print("Fade out the image")
})

// or

animate(duration: 3) {
    print("Fade out the image")
}
```



### Using Closures as Parameters when they Accept Parameters

```swift
func travel(action: (String) -> Void) {
    print("I'm getting ready to go.")
    action("London")
    print("I arrived!")
}

travel { (place: String) in
    print("I'm going to \(place) in my car")
}
```



Another example:

```swift
func getDirections(to destination: String, then travel: ([String]) -> Void) {
	let directions = [
		"Go straight ahead",
		"Turn left onto Station Road",
		"Turn right onto High Street",
		"You have arrived at \(destination)"
	]
	travel(directions)
}

getDirections(to: "London") { (directions: [String]) in
	print("I'm getting my car.")
	for direction in directions {
		print(direction)
	}
}
```



### Using closures as parameters when they return values

```swift
func travel(action: (String) -> String) {
    print("I'm getting ready to go.")
    let description = action("London")
    print(description)
    print("I arrived!")
}

travel { (place: String) -> String in
    return "I'm going to \(place) in my car"
}

// or, use shorthand (remove parameter type)
travel { place -> String in
    return "I'm going to \(place) in my car"
}

// even shorter (remove return type)
travel { place in
	   return "I'm going to \(place) in my car"    
}

// even shorter (remove return keyword)
travel { place in
	   "I'm going to \(place) in my car"    
}

// even shorter (use automatic name for parameter)
travel {
	   "I'm going to \($0) in my car"    
}


```



### Closures with Multiple Parameters

```swift
func travel(action: (String, Int) -> String) {
    print("I'm getting ready to go.")
    let description = action("London", 60)
    print(description)
    print("I arrived!")
}

travel {
    "I'm going to \($0) at \($1) miles per hour."
}
```



### Returning Closures from Functions

```swift
func travel() -> (String) -> Void {
    return {
        print("I'm going to \($0)")
    }
}

let result = travel()
result("London")
```



### Capturing Values

If you use external values inside your closure, Swift captures them and stores them so they can be modified even if you they don't exist anymore.

```swift
func travel() -> (String) -> Void {
  	// remains alive for this closure
    var counter = 1

    return {
        print("\(counter). I'm going to \($0)")
        counter += 1
    }
}

result("London")
```



## Structs

### Basics

A `struct` (structure) is one of the two ways Swift lets you design your own types.

This is a `struct` with one property:

```swift
struct Sport {
    var name: String
}
```



To use it:

```swift
var tennis = Sport(name: "Tennis")
print(tennis.name)

tennis.name = "Lawn tennis"
```



You can set defaults as well:

```swift
struct User {
	var name = "Anonymous"
	var age: Int
}
```



### Computed Properties

A **computed** property is a property that runs code to figure out its value.

It must have an explicit type. <u>It also must be a `var` not a `let`.</u>

```swift
struct Sport {
    var name: String
    var isOlympicSport: Bool

    var olympicStatus: String {
        if isOlympicSport {
            return "\(name) is an Olympic sport"
        } else {
            return "\(name) is not an Olympic sport"
        }
    }
}

let chessBoxing = Sport(name: "Chessboxing", isOlympicSport: false)
print(chessBoxing.olympicStatus)
```



### Property Observers

**Property observers** let you run code before or after any property changes. 

`didSet` and `willSet` are two examples:

```swift
struct Progress {
    var task: String
  	// Will always need to be a var, so it can change and proceed to didSet
    var amount: Int {
        didSet {
            print("\(task) is now \(amount)% complete")
        }
    }
}

// Every time the amount changes, we will see a string
var progress = Progress(task: "Loading data", amount: 0)
progress.amount = 30
progress.amount = 80
progress.amount = 100
```



A `willSet` is useful if you need to know the state of something before it changes. Useful for animations.

### Methods

A function inside a `struct` is called a **method**.

```swift
struct City {
    var population: Int

    func collectTaxes() -> Int {
        return population * 1000
    }
}

let london = City(population: 9_000_000)
london.collectTaxes()
```



### Mutating Methods

By default, Swift won’t let you write methods that change properties unless you specifically request it.

You need to add a `mutating` keyword:

```swift
struct Person {
    var name: String

    mutating func makeAnonymous() {
        name = "Anonymous"
    }
}

var person = Person(name: "Ed")
person.makeAnonymous()
```



Also, marking a method as `mutating` will stop the method from being called on constand sructs, even if the method doesn't change any properties. The keyword trumps the variable type.

### Properties and Methods of Strings

Strings are structs themselves. 

```swift
let string = "Do or do not, there is no try."

// Some methods
print(string.count)
print(string.hasPrefix("Do"))
print(string.contains("Do"))
print(string.uppercased())
print(string.sorted()) // Returns array
```



To check if a string is empty, use `if myString.isEmpty {}` instead of `if myString.count == 0 {}`. It's faster. [Source](https://www.hackingwithswift.com/articles/181/why-using-isempty-is-faster-than-checking-count-0)

### Properties and Methods of Arrays

Arrays are structs themselves. 

```swift
var toys = ["Woody"]

// Some methods
print(toys.count)
toys.append("Buzz")
toys.firstIndex(of: "Buzz")
toys.contains("Woody")
print(toys.sorted())
toys.insert(at: 0)
toys.remove(at: 0)
```



### Initializers

Initializers are special methods that provide different ways to create your struct. 

All structs come with one by default, called their **memberwise initializer**. If you create your own then you must give all propeties a value.

```swift
struct User {
    var username: String

  	// all properties need to have a value before init ends
    init() {
        username = "Anonymous"
        print("Creating a new user!")
    }
}

// Since the initializer takes no parameters, the struct needs to be created like this:
var user = User()
user.username = "twostraws"
```



### Referring to the Current Instance

`self` is useful when you create initializers with parameter names that are the same as a property.

It refers to the instance of the struct that is currently being used.

```swift
struct Person {
    var name: String

    init(name: String) {
        print("\(name) was born!")
        self.name = name
    }
}
```



### Lazy Properties

Adding `lazy` to a property keeps it from being created unless it's needed.

```swift
struct FamilyTree {
    init() {
        print("Creating family tree!")
    }
}

struct Person {
    var name: String
	  // Adding "lazy" creates the FamilyTree struct when it's first accessed.
    lazy var familyTree = FamilyTree()

    init(name: String) {
        self.name = name
    }
}

var ed = Person(name: "Ed")

// You will now see the "Creating family tree!" message.
ed.familyTree
```



### Static Properties and Methods

A **static** property is a property that is shared across all instances of the struct.

```swift
struct Student {
    static var classSize = 0
    var name: String

    init(name: String) {
        self.name = name
        Student.classSize += 1
    }
}
```

To use it, you need to read it from the struct:

```swift
print(Student.classSize)
```



### Private and Public Properties

If you want to stop a property from being accessed, add the **private** keywod:

```swift
struct Person {
    private var id: String

    init(id: String) {
        self.id = id
    }

    func identify() -> String {
        return "My social security number is \(id)"
    }
}
```



## Classes

There are 5 differences between Classes and Structs.

1. Classes never come with a memberwise initializer. You must always create your own initializer.

```swift
class Dog {
    var name: String
    var breed: String

    init(name: String, breed: String) {
        self.name = name
        self.breed = breed
    }
}

let poppy = Dog(name: "Poppy", breed: "Poodle")
```



2. You can create your own class based on an exisiting class. Also know as **class inheritance** or **subclassing**. The new class is called a **child** class.

```swift
class Dog {
    var name: String
    var breed: String

    init(name: String, breed: String) {
        self.name = name
        self.breed = breed
    }
}

// Create a new class
class Poodle: Dog {
    init(name: String) {
      	// You need to call super.init() for safety reasons.
        super.init(name: name, breed: "Poodle")
    }
}
```



Child classes can replace parent methods via **overriding**:

```swift
class Dog {
    func makeNoise() {
        print("Woof!")
    }
}

class Poodle: Dog {
    override func makeNoise() {
        print("Yip!")
    }
}
```



You can also prevent inheritence with the **final** keyword:

```swift
final class Dog {
    var name: String
    var breed: String

    init(name: String, breed: String) {
        self.name = name
        self.breed = breed
    }
}
```



3. When you copy a struct, you make two different copies. When you copy a class, they both point to the same thing.

```swift
class Singer {
    var name = "Taylor Swift"
}

var singer = Singer()
print(singer.name) // Taylor Swift

var singerCopy = singer
singerCopy.name = "Justin Bieber"

print(singer.name) // Justin Biever

```



4. Classes can have **deinitializers**. Code that gets run when an instance of a class is destroyed.

```swift
class Person {
    var name = "John Doe"

    init() {
        print("\(name) is alive!")
    }

    func printGreeting() {
        print("Hello, I'm \(name)")
    }
  
    deinit {
      print("\(name) is no more!")
	  }
}
```



5. If you have a constant class with a variable property, that property can be changed. No `mutating` keyword required.

   This is valid:

```swift
class Singer {
    var name = "Taylor Swift"
}

// constant class
let taylor = Singer()
taylor.name = "Ed Sheeran"

print(taylor.name) // Ed Sheeran
```

​	To prevent this from happening, the property should be a constant:

```swift
class Singer {
    let name = "Taylor Swift"
}
```



## Protocols and Extensions

### Protocol Basics

Protocols are a way of describing what properties and methods something must have--basically creating your own types.

You then tell Swift which types use that protocol – a process known as **adopting** or **conforming** to a protocol.

```swift
// All conforming types need to have an id that can be read ("get") or written ("set")
protocol Identifiable {
    var id: String { get set }
}

// Creating a struct that conforms to the Identifiable protocol
struct User: Identifiable {
    var id: String
}

// Creating a function that accepts any Identifiable object
func displayID(thing: Identifiable) {
    print("My ID is \(thing.id)")
}
```



### Protocol Inheritance

Protocols can inherit from one another. You can even inherit from multiple protocols at the same time.

```swift
protocol Payable {
    func calculateWages() -> Int
}

protocol NeedsTraining {
    func study()
}

protocol HasVacation {
    func takeVacation(days: Int)
}

// This Employee protocol brings the three together
protocol Employee: Payable, NeedsTraining, HasVacation { }

```



### Extensions

Extensions allow you to add methods to existing types, to make them do things they weren’t originally designed to do. 

Computer properties only. No stored properties.

```swift
// Extending the Int type to have a squared method
extension Int {
    func squared() -> Int {
        return self * self
    }
}

let number = 8
number.squared()
```



### Protocol Extensions

A protocol extension lets you extend a protocol to all conforming types.

Arrays and Sets conform to the `Collection` protocol. We can write an extension that adds a `summarize()` method:

```swift
let pythons = ["Eric", "Graham", "John", "Michael", "Terry", "Terry"]
let beatles = Set(["John", "Paul", "George", "Ringo"])

extension Collection {
    func summarize() {
        print("There are \(count) of us:")

        for name in self {
            print(name)
        }
    }
}

// both of these now work
pythons.summarize()
beatles.summarize()
```



### Protocol-Oriented Programming

**Protocol-oriented programming**  is the practice of designing your app architecture as a series of protocols, then using protocol extensions to provide default method implementations.

```swift
protocol Identifiable {
    var id: String { get set }
    func identify()
}

// Use a protocol extension to provide a default identity() method
extension Identifiable {
    func identify() {
        print("My ID is \(id).")
    }
}

// Now when we create a type that conforms to Identifiable
// it gets identify() automatically:
struct User: Identifiable {
    var id: String
}

let twostraws = User(id: "twostraws")
twostraws.identify()
```



## Optionals

An optional may have data, or no value at all (`nil`).

```swift
var age: Int? = nil
```



### Unwrapping Optionals

You need to check to see if a variable isn't `nil` before checking a property on it. Do this by assigning it to a new variable using `if let` syntax.

This process is know as **unwrapping**

```swift
if let unwrapped = name {
    print("\(unwrapped.count) letters")
} else {
    print("Missing name.")
}
```



### Unwrapping with Guard

A way to deal with problems at the start of your function.

Using a `guard let` instead of an `if let` keeps your unwrapped optional usable after the `guard` code, so no `if/else` statement needed.

```swift
func greet(_ name: String?) {
    guard let unwrapped = name else {
        print("You didn't provide a name!")
        return
    }

    print("Hello, \(unwrapped)!")
}
```



### Force Unwrapping

Swift will take the following code and turn `num` into an optional `Int` (`Int?`) since it isn't sure if `str` is actually an `Int`.

```swift
let str = "5"
let num = Int(str)
```

If you know that the `str` will be a number, you can prevent the optional with an `!`:

```swift
let num = Int(str)!
```

This is also know as the **crash operator** since it can cause crashes if you're wrong.



### Implicitly Unwrapped Optionals

**Implicitly Unwrapped Optionals** can be used without the need to unwrap them using `if let` or `guard let`. Create them like this:

```swift
let age: Int! = nil
```

Do this if you know a variable will get a value before it is used. You won't have to bother writing unwrapping statements.



### Nil Coalescing

The **nil coalescing operator** unwraps an optional and returns the value inside if there is one. If there is not, it returns a default value instead.

```swift
func username(for id: Int) -> String? {
    if id == 1 {
        return "Taylor Swift"
    } else {
        return nil
    }
}

// username() returns nil, so user is set to "Anonymouse"
let user = username(for: 15) ?? "Anonymous"
```



### Optional Chaining



### Optional Try

### Failable Initializers

### Typecasting