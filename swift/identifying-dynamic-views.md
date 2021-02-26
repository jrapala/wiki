# Identifying Dynamic Views

If an object conforms to the `Identifiable` protocol, it will automatically use its `id` property for uniquing.

Otherwise, we can use a keypath for a property we know will be unique.

Lastly, if we don't have either, we can often use `\.self`

```swift
List {
    ForEach([2, 4, 6, 8, 10], id: \.self) {
        Text("\($0) is even")
    }
}
```

`\.self` means "the whole object." Swift computes the hash value of the struct. Managed objects and strings and ints all conform to `Hashable`, which means it can generate has values, and you can use `\.self`for it.

You can make a custom type conform to `Hashable` as well:

```swift
struct Student: Hashable {
    let name: String
}

struct ContentView: View {
    let students = [Student(name: "Harry Potter"), Student(name: "Hermione Granger")]

    var body: some View {
        List(students, id: \.self) { student in
            Text(student.name)
        }
    }
}
```

Here, Swift calculates hash values ot each property, and combines them into one hash for the whole struct. A problem will occur is we have a student with the same name.