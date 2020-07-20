# SwiftUI

## About SwiftUI

Works for iPad, Mac, Apple TV, iPhone and Watch. Elements render appropriatly for their device (e.g. a Picker on Mac vs iOS).

Uses [Declarative Syntax](https://developer.apple.com/xcode/swiftui/).

Using provided font sizes scales text automatically to fit device / accessibility needs.

## Getting Started

1. New XCode Project
2. Select Single View App
3. In project options, the Organization Identifier is usually your domain in reverse (e.g. com.google). The language should be Swift and the User Interface should be SwiftUI.
4. Import app icons, colors (light and dark appearances), etc.. into your `Assets.xcassets` directory. SVG assets should be in a PDF format. You'll only need a 1x version for PDFs.
5. Start writing code in `ContentView.swift`

## Development

Press `Resume` on top of Preview view to render content.

`Cmd + Click` on an element in the Preview and/or code lets you use the SwiftUI inspector, where you can modify styles,  embed within stacks, or extract subviews.

`Shift + Cmd + L` brings up an Insert menu where you can search for elements/modifiers.

You can drag and drop assets directly into the Preview.

You can Pin the Preview so you'll always see the same screen as you make code changes.

`Ctrl + I` cleans up code.

### Keyboard Shortcuts

- `Cmd + Click`: Contextual menu for code or UI.
- `Option + Click`: Quick info for code.
- `Cmd + 0`: Show/hide Navigator.
- `Cmd + Shift + L`: Insert new element.
- `Cmd + R`: Run the app.
- `Cmd + . `: Stop the app.

### Dark Mode

```Swift
ContentView(courses: testData)
    .environment(\.colorScheme, .dark)
```



## Layout

All elements are centered by default.

SwiftUI has very good default values.

### Types of Stacks

- `VStack` Vertical Stack
- `HStack` Horizontal Stack
- `ZStack` Depth Stack (3D). Arranged from rear to front.

Stacks can be modified: `VStack(spacing: 20) {}` or `ZStack(alignment: .topLeading) {}`

### Layout Modifiers

- A `.frame(width: Number, height: Number, alignment?: modifier)` modifier added to a stack lets you specify a maximum fixed size of the stack.
- `.frame(maxWidth: .infinity)` forces a stack to take max width instead of using a Spacer
- A `.resizable()` modifier added to an image resizes it for a frame. Add `.aspectRatio(contentMode: .fill)` to preserve aspect ratio. Add another frame to constrain it inside a frame.
- `.cornerRadius(20)` adds a corner radius that clips content
- `.shadow(radius: 20)`
- `.padding()` adds a default padding (16). Or can be modified: `.padding(.horizontal: 20)`
- `.background(Color.black)` 
- `.foregroundColor(Color("accent"))`
- `.offset(x: Number, y: Number)` for moving elements away from center.
- `.opacity(0.1)`
- `.blur(radius: 20)` for directing focus (very perfomant on iOS)

## Basic Elements

- A `Spacer()` will take up the remaining space between two elements (lets you push two elements away from each other). Children of stacks take up the minimum size of the element.
- `Image("ImageName")`
- `Text("Hello World")`
- `Rectangle()`
- `Toggle()`
- `Picker()`
- `Stepper()`
- `Slider()`

Icons can be imported from [SF Symbols](https://developer.apple.com/design/human-interface-guidelines/sf-symbols/overview/):

```swift
Image(systemName: item.icon)
    .imageScale(.large)
    .foregroundColor(.blue)
    .frame(width: 32, height: 32)
```

### Text Modifiers

- `.font(.subheadline)`
- `.fontWeight(.bold)`
- `.lineSpacing(4)`
- `.multilineTextAlignment(.center)` for centering text

## Components

Extract stacks to their own components with `Cmd + Click` > `Extract Subview`

Modifiers can be added to instances of a component.

## Effects

- `.scaleEffect(0.5)`
- `.rotationEffect(Angle(degrees: 10))` or `.rotationEffect(.degrees(10))`
- `.rotation3DEffect(Angle(degrees: 10), axis: (x: 10, y: 0, z: 0))` for a perspective transform.
- `.blendMode(.hardLight || .softLight || .overlay)`

## State



## Additional Resources

- [Apple Tutorials](https://developer.apple.com/tutorials/swiftui/tutorials)
- [Hacking with Swift](https://www.hackingwithswift.com/quick-start/swiftui)
- [Miscellaneous Resources](https://github.com/Juanpe/About-SwiftUI)
- [iOS Dev Weekly Newsletter](https://iosdevweekly.com/)
- [iOS Goodies Newsletter](https://ios-goodies.com/)
- [design+code YouTube](https://www.youtube.com/channel/UCTIhfOopxukTIRkbXJ3kN-g)

