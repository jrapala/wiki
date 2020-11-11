# GeometryReader

The `GeometryReader` can help you make an image fit the width of a user's screen.

It is a `View` just like an others, but give you a `GeometryProxy` object to user:

```swift
// an image that fits the wide of a user's screen
struct ContentView: View {
    var body: some View {
        GeometryReader { geo in
            Image("alpacaAndMe")
                .resizable()
                .aspectRatio(contentMode: .fit)
                .frame(width: geo.size.width)
        }
    }
}
```

