# SwiftUI

## About SwiftUI

Works for iPad, Mac, Apple TV, iPhone and Watch. Elements render appropriatly for their device (e.g. a Picker on Mac vs iOS).

Uses [Declarative Syntax](https://developer.apple.com/xcode/swiftui/).

Using provided font sizes scales text automatically to fit device / accessibility needs.

Unlike developing for web, when developing with SwiftUI, you start with content first, and then build a parent container around it.

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

### Smooth Corners

The `.cornerRadius()` modifier clips content. Use `.clipShape()` to preserve content:

```Swift
.clipShape(RoundedRectange(cornerRadius: 20, style: .continuous))
```

These can be animated!



## Basic Elements

- A `Spacer()` will take up the remaining space between two elements (lets you push two elements away from each other). Children of stacks take up the minimum size of the element.
- `Image("ImageName")`
- `Text("Hello World")`
- `Rectangle()`
- `Toggle()`
- `Picker()`
- `Stepper()`
- `Slider()`



### Text Modifiers

- `.font(.subheadline)`
- `.font(.system(size: 20, weight: .bold, design: .default)` to customize (`.default` is SF Pro)
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

### Initialize State

`@State var show = false`

### Set State

```swift
.onTapGesture {
    self.show.toggle()
}
```

### Use State

```swift
BackCardView()
	.rotationEffect(Angle(degrees: show ? 0 : 5))
```



## Gestures

- `onTapGesture`

- ```swift
  .gesture(
  		DragGesture().onChanged { value in
  				self.viewState = value.translation
      }
    	.onEnded { value in
          self.viewState = .zero
      }
  )
  ```



Gestures can have multiple events like `onChanged` and `onEnded`. The events return values that can be translated to `CGSize`.

## Animations

`.animation(.easeInOut(duration: 0.3))`

### Transition Options

In additional to normal timing options, there is a `.default` timing option that is similar to `.easeInOut`

Physics-based option: `animation(.spring(response: 0.3, dampingFraction: 0.6, blendDuration: 0))`

- The lower the  `.response`, the quicker the animation
- The `dampingFraction` regards the amount of bounce
- The `blendDuration` regards animation queuing???

### Custom Animations

You can use a preset animation, or customize a preset by pulling in an `Animation` type first:

```swift
.animation(
		Animation
  		.default
  		.delay(0.1)
  		// Twice as fast as before
		  // .speed(2)
)
```

#### Animation Modifiers

- `.timingCurve(0.2, 0.8, 0.2, 1, duration: 0.8)` for cubic-bezier
- `.repeatCount(3, autoreverses: false)`
- `.repeatForever()`



### Animation States

You may often store animation states as a `CGSize` type. For example, `@State var viewState = CGsize.zero`. This type stores an `x` and a `y` position as `width` and `height`. Use it to store the current location of your drag gesture.



## Conditionals

```swift
if (self.bottomState.height < -100 && !self.showFull) || (self.bottomState.height < -250 && self.showFull) {
    self.showFull = true
}
```



## Debugging

Instead of using logs, log value directly to a Text element: `Text("\(bottomState.height)").offset(y: -300)`



## Adding New Screens

To create a new screen, right click on `ContentView.swift` and selected `New File`. Select `SwiftUI` and name it.



## Using SF Symbols

Download the [SF Symbols app](https://developer.apple.com/design/). It has over 1000 icons. All you need is the name of the icon. You can style it like Text.

```Swift
Image(systemName: "gear")
		.font(.system(size: 20, weight: .light))
		.imageScale(.large)
		.frame(width: 32, height: 32)
```



## Components with Props

### Component

```
RowWithIcon(icon: "gear")
```



### Using the Component

Type the props on top:

```swift
struct RowWithIcon: View {
    var icon: String

    var body: some View {
        HStack {
            Image(systemName: icon)
							...

```

You can set default values: `var icon = "gear"`

## Additional Resources

- [Apple Tutorials](https://developer.apple.com/tutorials/swiftui/tutorials)
- [Hacking with Swift](https://www.hackingwithswift.com/quick-start/swiftui)
- [Miscellaneous Resources](https://github.com/Juanpe/About-SwiftUI)
- [iOS Dev Weekly Newsletter](https://iosdevweekly.com/)
- [iOS Goodies Newsletter](https://ios-goodies.com/)
- [design+code YouTube](https://www.youtube.com/channel/UCTIhfOopxukTIRkbXJ3kN-g)

