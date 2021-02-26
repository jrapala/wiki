# Comparable

`Comparable` conformance allows you to set sorting rules.

An `Int` already comforms to `Comparable`. `.sorted()` can be used on an array of them.



## Create a custom `.sorted()` method

1. Add the `Comparable` conformance to the definition of `User`.
2. Add a method called `<` that takes two users and returns true if the first should be sorted before the second.

```swift
struct User: Identifiable, Comparable {
    let id = UUID()
    let firstName: String
    let lastName: String

  	// operator overloading: adding functionality to an existing operator
  	// lhs: left-hand side
  	// rhs: right-hand side
    static func < (lhs: User, rhs: User) -> Bool {
        lhs.lastName < rhs.lastName
    }
}
```



This now works:

```swift
let users = [
    User(firstName: "Arnold", lastName: "Rimmer"),
    User(firstName: "Kristine", lastName: "Kochanski"),
    User(firstName: "David", lastName: "Lister"),
].sorted()
```



### An Extension That Allows Any Type of Multiplication

`Int`s can now be multiplied with `CGFloat`s and `Double`s.

```swift
// All integere types live in BinaryInteger
extension BinaryInteger {
  	// If Self is Int16, then Int16 is used.
    static func * (lhs: Self, rhs: CGFloat) -> CGFloat {
        return CGFloat(lhs) * rhs
    }

    static func * (lhs: CGFloat, rhs: Self) -> CGFloat {
        return lhs * CGFloat(rhs)
    }

    static func * (lhs: Self, rhs: Double) -> Double {
        return Double(lhs) * rhs
    }

    static func * (lhs: Double, rhs: Self) -> Double {
        return lhs * Double(rhs)
    }
}
```



Note, accuracy will be forfeited. A test:

```swift
let exampleInt: Int64 = 50_000_000_000_000_001
print(exampleInt)

let result = exampleInt * 1.0
print(String(format: "%.0f", result))
```



