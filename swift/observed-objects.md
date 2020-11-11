# @ObservedObject

Swift gives us `@ObservedObject` and `@EnvironmentObject` to help us deal with data shared across more than one view.

We can use it to create objects that can be used in multiple views.



## Limitations of @State

This code won't work as intended.

`@State` tracks local structs rather than external classes. 

```swift
class User {
    var firstName = "Bilbo"
    var lastName = "Baggins"
}

struct ContentView: View {
    @State private var user = User()

    var body: some View {
        VStack {
            Text("Your name is \(user.firstName) \(user.lastName).")

            TextField("First name", text: $user.firstName)
            TextField("Last name", text: $user.lastName)
        }
    }
}
```



## Triggering Reloads

Adding an `@Published` property observer notifies any views watching a class that a change has happened, so it can reload. These can only be used on types that conform to the `ObservableObject` protocal.

```swift
class User: ObservableObject {
    @Published var firstName = "Bilbo"
    @Published var lastName = "Baggins"
}
```



These send notifications to values with the `@ObservedObject` property wrapper:

```swift
@ObservedObject var user = User()
```



`@Published` announces changes from a property; `@ObservedObject` watches an observed object for changes.

