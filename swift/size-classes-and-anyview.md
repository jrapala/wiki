# Size Classes and AnyView

Size classes are Apple's way of telling us how much space we have for our views.

There are two size classes: "compact" (landscape) and "regular" (portrait), though these aren't consistent (sometimes small windows on iPad come up as "compact")



## Type Erasure

To return a certain view for a certain size class, we need to use **type erasure**.

**AnyView** is a **type erased wrapper**. Here, Swift returns AnyView or AnyView instead of a VStack or HStack. AnyView is one type, so Swift knows what type to return.

Note: Use rarely, due to performance reasons. 

```swift
struct ContentView: View {
    @Environment(\.horizontalSizeClass) var sizeClass

    var body: some View {
        if sizeClass == .compact {
            return AnyView(VStack {
                Text("Active size class:")
                Text("COMPACT")
            }
            .font(.largeTitle))
        } else {
            return AnyView(HStack {
                Text("Active size class:")
                Text("REGULAR")
            }
            .font(.largeTitle))
        }
    }
}
```



## AnyView Alternative

Instead of an `AnyView`, you could use a `Group`:

```swift
struct ContentView: View {
    var body: some View {
        Group {
            if Bool.random() {
                Text("Hello, World!")
                    .frame(width: 300)
            } else {
                Text("Hello, World!")
            }
        }
    }
}
```

Avoid doing this though. It's a case of premature optimization. The intent behind the code is hard to figure out.

Groups are typically used for:

- Breaking the 10-child view limit
- Delegating layout to a parent container. It can be embedded within an HStack or VStack.
- Lets us apply one set of modifiers to many views at once.

AnyViews signify intent for type erasure. They also allow for arrays.



### Convenience Extension for Type Erasure

```swift
extension View {
    func erasedToAnyView() -> AnyView {
        AnyView(self)
    }
}
```

```swift
Text("Hello World")
    .font(.title)
    .erasedToAnyView()    
```

