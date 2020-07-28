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
- `.frame(maxWidth: .infinity)` forces an element/stack to take max width instead of using a Spacer
- A `.resizable()` modifier added to an image resizes it for a frame. Add `.aspectRatio(contentMode: .fill)` to preserve aspect ratio. Add another frame to constrain it inside a frame.
- `.cornerRadius(20)` adds a corner radius that clips content
- `.shadow(radius: 20)`
- `.shadow(color: Color.black.opacity(0.2), radius: 20, x: 0, y: 20)` customized shadow
  - For a realistic shadow of a bright color: `.shadow(color: Color("foregroundColor").opacity(0.3), radius: 20, x: 0, y: 20)`
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
- `.uppercased()`



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



## Color Literals & Gradients

Backgrounds don't have the be Colors (`.background(Color.white)`). They can be Views as well (which include gradients or a `Color.color`).

```swift
.background(LinearGradient(gradient: Gradient(colors: [Color.red, Color.blue]), startPoint: .top, endPoint: .bottom))
```

The `Color.red` can be replaced with `Color(Color Literal)` to allow you to input a hex code or pick from a color picker (double click on the literal)



## Overlays

To add an overlay after a `.clipShape`:

```swift
.overlay(
  Image("Avatar")
  .resizable()
  .aspectRatio(contentMode: .fill)
  .frame(width: 60, height: 60)
  .clipShape(Circle())
  .offset(y: -150)
)
```



## Buttons

```swift
Button(action: {}) {
	Text("Button")
}
```

- By default, it will have Text inside.

- A button has default behavior, such as tap animation. All content within the button will be tinted automatically.
- You can use an Image instead of Text, however you need to remove the tint with `.renderingMode(.original)`
- In the action, you can toggle states `(action: { self.showProfile.toggle() })`



## Animation Between Screens (Simple)

If you need to show another screen, there's no need to import it. Just add in the view.

`.edgeIgnoringSafeArea(.all)` should be added to whichever views you wish to ignore the safe area. Don't add it to a parent of many views. Some views need the safe area.

If you need to pad away from top, 44 is the size of the status bar. Make sure it's adaptive per device.

For lightweight screens, you can change screens using state and a button toggle for example.



To add gestures above a smaller element of the screen, add a background with a .001 opacity. A zero opacity will prevent you from interacting with it.

![Example](https://juliette-images.s3.us-east-2.amazonaws.com/public/swift001.png)



## Binding States

If you create a new component that uses outside state, you need to bind that state to the component.

```swift
// Parent
struct Home: View {
	@State var showProfile = false
	
	var body: some View {
    // 2. Pass the state (a binding) and add a $
		AvatarView(showProfile: $showProfile)
	}
}

// Child
struct AvatarView: View {
  // 1. Declare a binding instead of a state
	@Binding var showProfile: Bool
	
	var body: some View {
		Button(action: { self.showProfile.toggle }) {
			...
		}
	}
}
```



If you need to bind to a file with a preview, you need to specify default settings:

```swift
struct HomeView_Previews: PreviewProvider {
    static var previews: some View {
        HomeView(showProfile: .constant(false))
    }
}
```



## Detecting Screen Size

Declare `let screen = UIScreen.main.bounds` anywhere in the app. 

To use it: `.offset(y: showProfile ? 0 : screen.height)`



## Looping

`Cmd + Click` on your view and select `Repeat`. It will create 5 copies of the same element:

```
ForEach(0 ..< 5) { item in
	SectionView()
}
```



## ScrollView

Lets you set a frame that you can scroll. Everything depends on the content.

```
ScrollView {
	ForEach(0 ..< 5) { item in
		SectionView()
	}
}
```

For a horizontal ScrollView: `ScrollView(.horizontal)`. You may need to embed the content in an HStack.

To remove scrollbar: `ScrollView(showsIndicators: false)`



## Working with Data

### Data Models

You can add a data model to any file. 

`UUID()` can help you create a unique id.

```swift
struct Section: Identifiable {
    var id = UUID()
    var title: String
    var text: String
    var logo: String
    var image: Image
    var color: Color
}
```



### Array of Data

You can skip autogenerated data like the id:

```swift
let sectionData = [
    Section(title: "Prototype Designs in SwiftUI", text: "18 Sections", logo: "Logo1", image: Image("Card1"), color: Color("card1")),
		...
]

```

You can now loop through it:

```swift
ForEach(sectionData) { item in
	SectionView(section: item)
}
```



In the SectionView:

```swift
struct SectionView: View {
  var section: Section
  ...
  Text(section.title)
}
```



## Image Literals

Pick your image straight from your code!

`Image(uiImage: UIImage)`

Replace `UIImage` with `Image Literal`



## Additional Resources

- [Apple Tutorials](https://developer.apple.com/tutorials/swiftui/tutorials)
- [Hacking with Swift](https://www.hackingwithswift.com/quick-start/swiftui)
- [Miscellaneous Resources](https://github.com/Juanpe/About-SwiftUI)
- [iOS Dev Weekly Newsletter](https://iosdevweekly.com/)
- [iOS Goodies Newsletter](https://ios-goodies.com/)
- [design+code YouTube](https://www.youtube.com/channel/UCTIhfOopxukTIRkbXJ3kN-g)

