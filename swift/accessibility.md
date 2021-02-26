# Accessibility

SwiftUI gives us a huge amount of functionality for free, because its layout system of `VStack` and `HStack` naturally forms a flow of views. However, it isn’t perfect, and any time you can add some extra information to help out the iOS accessibility system it’s likely to help.



## Adding Labels to Views

When given an image, VoiceOver uses the image’s filename as the text to read out.

For this code, VoiceOver reads “Kevin Horstmann one four one seven zero five”, followed by "Image"

```swift
struct ContentView: View {
    let pictures = [
        "ales-krivec-15949",
        "galina-n-189483",
        "kevin-horstmann-141705",
        "nicolas-tissot-335096"
    ]

    @State private var selectedPicture = Int.random(in: 0...3)

    var body: some View {
        Image(pictures[selectedPicture])
            .resizable()
            .scaledToFit()
            .onTapGesture {
                self.selectedPicture = Int.random(in: 0...3)
            }
    }
}
```

We can add modifiers to fix this, such as:

- `.accessibility(label:)` is read immediately, and should be a short piece of text that gets right to the point. If this view deletes an item from the user’s data, it might say “Delete”. Ideally, one or two words.
- `.accessibility(hint:) ` is read after a short delay, and should provide more details on what the view is there for. It might say “Deletes an email from your inbox”, for example.
- `.accessibility(addTraits: .isButton)` describes how views work.
- `.accessibility(removeTraits: .isImage)` removes image traits.
- `.accessibilityElement(children: .ignore)` does not allow things inside the view to be accessible, but the parent view is.

Accessibility labels are read before the hint.



Updated code:

```swift
struct ContentView: View {
    let pictures = [
        "ales-krivec-15949",
        "galina-n-189483",
        "kevin-horstmann-141705",
        "nicolas-tissot-335096"
    ]
    
    let labels = [
        "Tulips",
        "Frozen tree buds",
        "Sunflowers",
        "Fireworks",
		]

    @State private var selectedPicture = Int.random(in: 0...3)

    var body: some View {
        Image(pictures[selectedPicture])
            .resizable()
            .scaledToFit()
            .onTapGesture {
                self.selectedPicture = Int.random(in: 0...3)
            }
      	// Add this modifier to read the correct label, no matter what picture is selected.
	      .accessibility(label: Text(labels[selectedPicture]))
      	// Add this modifier to say that the image is also a button
	      .accessibility(addTraits: .isButton)
      	// Add this modifier to remove the "Image" callout, if desired
      	.accessibility(removeTraits: .isImage)
    }
}
```



## Hiding and Grouping Accessibility Data

We want to remove as much clutter as possible to ensure users can navigate quickly. Some ways to ensure this:

- Marking images as being unimportant for VoiceOver.
- Hiding views from the accessibility system.
- Grouping several views as one.



### Hiding Elements

`Image(decorative: "bullet point")` will remove be ignored by VoiceOver unless it has important traits (like `.isButton`) or a tap gesture that works or has a label or hint.

`Image(decorative: "character").accessibility(hidden: true)` completely hides it. Do it if the view is offscreen and not useful.



### Grouping Elements

This modifier has VoiceOver read both strings, even if only one is selected.

```swift
VStack {
    Text("Your score is")
    Text("1000")
        .font(.title)
}
.accessibilityElement(children: .combine)
```



To prevent a pause between elements, use this better solution:

```swift
VStack {
    Text("Your score is")
    Text("1000")
        .font(.title)
}
.accessibilityElement(children: .ignore)
.accessibility(label: Text("Your score is 1000"))
```



## Reading the Value of Controls

Swift reads user interface controls, but adds defaults. All Sliders are read as percentages. This can be overridden with the `accessibility(value:)` modifier.

```swift
@State private var estimate = 25.0

var body: some View {
    Slider(value: $estimate, in: 0...50)
        .padding()
			  .accessibility(value: Text("\(Int(estimate))"))
}
```

