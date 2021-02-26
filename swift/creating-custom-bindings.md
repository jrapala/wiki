# Creating Custom Bindings

**Problem:** You want to print a message every time a value wrapped in a `@State` property wrapper changes.

**Solution:** Create a custom binding. Use the `Binding` struct directly to run code when a value is read or written.

```swift
struct ContentView: View {
    @State private var blurAmount: CGFloat = 0

    var body: some View {
        let blur = Binding<CGFloat>(
            get: {
                self.blurAmount
            },
            set: {
                self.blurAmount = $0
                print("New value is \(self.blurAmount)")
            }
        )

        return VStack {
            Text("Hello, World!")
                .blur(radius: blurAmount)

          	// Note, instead of using $blurAmount, we just use blur
          	// We created the binding directly so we no longer need to get a two-way binding
            Slider(value: blur, in: 0...20)
        }
    }
}
```



The basic initializer for a `Binding` looks like this:

```swift
init(get: @escaping () -> Value, set: @escaping (Value) -> Void)
```

The initalizer takes two closures: a getter and setter. `Value` is a placeholder for whatever we're storing (e.g. `CGFloat`. Marking the closures with `@escaping` means that the struct stores them for use later on.



You can do whatever you want in a custom binding, as loong as you return a value from `get`. For example, you can update `UserDefaults` every time a value is changed in the `set` closure.



