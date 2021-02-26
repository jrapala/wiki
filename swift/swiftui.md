# SwiftUI

[TOC]

## About SwiftUI

Works for iPad, Mac, Apple TV, iPhone and Watch. Elements render appropriatly for their device (e.g. a Picker on Mac vs iOS).

Uses [Declarative Syntax](https://developer.apple.com/xcode/swiftui/).

Using provided font sizes scales text automatically to fit device / accessibility needs.

Unlike developing for web, when developing with SwiftUI, you start with content first, and then build a parent container around it.

It utilizes **declarative user interface design**. This means we say *what* we want rather than say *how* it should be done. If we want a Picker with some values inside, it is down to SwiftUI to decide whether it should be a wheel picker or the sliding view.



## Getting Started

1. New XCode Project
2. Select Single View App
3. In project options, the Organization Identifier is usually your domain in reverse (e.g. com.google). The language should be Swift and the User Interface should be SwiftUI.
4. Import app icons, colors (light and dark appearances), etc.. into your `Assets.xcassets` directory. SVG assets should be in a PDF format. You'll only need a 1x version for PDFs.
5. Start writing code in `ContentView.swift`



## Structure of a SwiftUI App

For a new project, in the **Project Navigator**, you will see these files:

- `AppDelegate.swift` contains code for managing your app. It used to be common to add code here, but now it's rare.
- `SceneDelegate.swift` contains code for launching one window in your app. Useful for iPad where you can have multiple instances of your app open at the same time.
- `ContentView.swift` contains the initial UI for your app
- `Assets.xcassets` is an asset catalog--a collection of images, colors, icons, iMessage stickers, etc..
- `LaunchScreen.storyboard` is a visual editor for creating a small piece of UI when your app is launching
- `Info.plist` is a collection of values that describe to the system how your app works (version, supported device orientations, etc..)
- `Preview Content` is a group with `Preview Assets.xcassets` inside. This is another asset catalog for example images you may want to use when you're designing your UI.



## ContentView.swift Boilerplate

```swift
// Import all of the functionality in the SwiftUI framework
import SwiftUI

// ContentView is a struct that conform to the View protocol. 
// View comes from SwiftUI - it is the basic protocol that must be adopted
// by anything you want to draw on the screen.
struct ContentView: View {
  	// A property called body that has a 'some View' type--an opaque result type.
  	// It conforms to the View protocol, but 'some' ensures that one specific
  	// sort of view must be sent back from this property.
  	//
  	// The view protocol has only one requirement - it must have a computed property
  	// called body that returns some View (and only one View).
    var body: some View {
      	// Creates a text view with the string "Hello World"
        Text("Hello World")
    }
}

// A struct that conforms to the PreviewProvider protocol.
// Won't be part of your final app. It's for Xcode to use so it can show 
// a preview of your UI in the canvas.
// 
// Canvas only works on Catalina. Otherwise, use a simulator.
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```





## Development

Press `Resume` on top of Preview view to render content.

`Cmd + Click` on an element in the Preview and/or code lets you use the SwiftUI inspector, where you can modify styles,  embed within stacks, or extract subviews.

`Shift + Cmd + L` brings up an Insert menu where you can search for elements/modifiers.

You can drag and drop assets directly into the Preview.

You can Pin the Preview so you'll always see the same screen as you make code changes.

Swift limits 10 children inside a parent. If you need to add more you need to wrap in a `Group {}`



### Keyboard Shortcuts

- `Cmd + Click`: Contextual menu for code or UI.
- `Option + Click`: Quick info for code.
- `Cmd + 0`: Show/hide Navigator.
- `Cmd + Shift + L`: Insert new element.
- `Cmd + R`: Run the app.
- `Cmd + . `: Stop the app.
- `Option + Cmd + P`: Resume the app
- `Ctrl + I`: Clean up code



### Dark Mode

```Swift
ContentView(courses: testData)
    .environment(\.colorScheme, .dark)
```



## Layout

All elements are centered by default.

SwiftUI has very good default values.

When you create a `View`, remember that **nothing is behind it.** If you want something behind it, you must use `.frame(maxWidth: .infinity, maxHeight: .infinity)`.



### Types of Stacks

- `VStack` Vertical Stack
- `HStack` Horizontal Stack
- `ZStack` Depth Stack (3D). Arranged from rear to front.

Stacks can be modified: `VStack(spacing: 20) {}` or `ZStack(alignment: .topLeading) {}`



### Layout Modifiers

Modifiers create new structs with that modifier applied, rather than just setting a property on the view. 

So, order matters.



Some modifiers are environmental instead of regular. You'll have to experiment to understand which modifier falls into which category.



- A `.frame(width: Number, height: Number, alignment?: modifier)` modifier added to a stack lets you specify a maximum fixed size of the stack.

- `.frame(maxWidth: .infinity)` forces an element/stack to take max width instead of using a Spacer

- A `.resizable()` modifier added to an image resizes it for a frame. Add `.aspectRatio(contentMode: .fill)` to preserve aspect ratio. Add another frame to constrain it inside a frame.

- `.cornerRadius(20)` adds a corner radius that clips content

- `.shadow(radius: 20)`

- `.shadow(color: Color.black.opacity(0.2), radius: 20, x: 0, y: 20)` customized shadow

  - For a realistic shadow of a bright color: `.shadow(color: Color("foregroundColor").opacity(0.3), radius: 20, x: 0, y: 20)`

  - For a two-source-of-light shadow (double drop shadow, one is sharp, one is more blurred):

    - ```swift
      .shadow(color: Color.black.opacity(0.1), radius: 1, x: 0, y: 1)
      .shadow(color: Color.black.opacity(0.2), radius: 10, x: 0, y: 10)
      ```

- `.padding()` adds a default padding (16). Or can be modified: `.padding(.horizontal: 20)`

- `.background(Color.black)` 

- `.foregroundColor(Color("accent"))`

- `.offset(x: Number, y: Number)` for moving elements away from center.

- `.opacity(0.1)`

- `.blur(radius: 20)` for directing focus (very perfomant on iOS)

- `.stroke(content style: StrokeStyle(lineWidth: CGFloat, lineCap: CGLineCap, lineJoin: CGLineJoin, miterLimit: CGFloat, dash: CGFloat, dashPhase: CGFloat))`

  - lineWidth: width of line
  - lineCap: only visible if gap between a dash or if trimmed
  - lineJoin:
  - miterLimit:
  - dash:
  - dashPhase:

- `.trim(from: 0.2, to: 1)`: lets you trim, for example, a Circle() (only 80% visible)

  

### Smooth Corners

The `.cornerRadius()` modifier clips content. Use `.clipShape()` to preserve content:

```Swift
.clipShape(RoundedRectange(cornerRadius: 20, style: .continuous))
```

These can be animated!



### Conditional Modifiers

```swift
struct ContentView: View {
    @State private var useRedText = false

    var body: some View {
        Button("Hello World") {
            // flip the Boolean between true and false
            self.useRedText.toggle()            
        }
        .foregroundColor(useRedText ? .red : .blue)
    }
}
```



## Basic Elements

- A `Spacer()` will take up the remaining space between two elements (lets you push two elements away from each other). Children of stacks take up the minimum size of the element.

- `Image("ImageName")`

  - `Image(decorative: "pencil")` to remove screen reader callout
  - `Image(systemName: "pencil")` to use SF Symbol icon

- `Text("Hello World")`

  - You can set specifiers to prevent long Doubles:

    ```swift
    // two decimal places
    Text("$\(totalPerPerson, specifier: "%.2f")")
    ```

    ```swift
    // discard insignificant zeroes
    Text("\(sleepAmount, specifier: "%g") hours")
    ```

- `TextField("Amount", text: $checkAmount)`
  
  - You can modify the keyboard with `.keyboardType(.decimalPad)`. Note, it doesn't change the type of content allowed.
  
- `Rectangle()`

- `Circle()`

- `Toggle()`

- `Picker()`
  
  - A segmented picker can be created with the `.pickerStyle(SegmentedPickerStyle())` modifier.![](https://juliette-images.s3.us-east-2.amazonaws.com/public/swift006.png)
  
- `Stepper()`

  - ```swift
    // You can set a range and a step in a Stepper
    Stepper(value: $sleepAmount, in: 4...12, step: 0.25) {
        Text("\(sleepAmount) hours")
    }
    ```

- `Slider()`

- `Form { }`
  
  - Note, adding elements inside a form changes how they would normally look
  
- `Section { }` to create sections
  
  - Accepts a header, e.g. `Section(header: Text("Title") {`
  
- `Alert(title: Text("Hello SwiftUI!"), message: Text("This is some detail message"), dismissButton: .default(Text("OK"))))`

- `DatePicker()`

  ```swift
  @State private var wakeUp = Date()
  
  var body: some View {
  	  //	This "in" range is for any dates in the future
      DatePicker("Please enter a date", selection: $wakeUp, displayedComponents: .hourAndMinute, in: Date()...)
    		// optional modifier to hide the label -- still read by VoiceOver
    		.labelsHidden()
  }
  ```

  - The look of a `DatePicker` can be changed by nesting it in a `Form`



Some of these are **primitive views**,  which conform to a view but return fixed content instead of rendering another view. Examples: Text, Image, Color, Spacer



### Text Modifiers

- `.font(.subheadline)`
- `.font(.system(size: 20, weight: .bold, design: .default)` to customize (`.default` is SF Pro)
- `.fontWeight(.bold)`
- `.lineSpacing(4)`
- `.multilineTextAlignment(.center)` for centering text
- `.uppercased()`



## Components

Extract stacks to their own components with `Cmd + Click` > `Extract Subview`

**Modifiers** can be added to instances of a component. A modifier is a method that returns a new instance of whatever you use them on.



## Effects

- `.scaleEffect(0.5)`
- `.rotationEffect(Angle(degrees: 10))` or `.rotationEffect(.degrees(10))`
- `.rotation3DEffect(Angle(degrees: 10), axis: (x: 10, y: 0, z: 0))` for a perspective transform.
- `.blendMode(.hardLight || .softLight || .overlay)`



## State

### Initialize State

Since `ContentView` is a struct, it is *immutable*. When creating struct methods that want to change properties. However, Swift doesn’t let us make mutating computed properties, which means we can’t write `mutating var body: some View`.

To overcome this, Swift gives us a **property wrapper**: a special attribute we can place before our properties to change their functionality. `@State` is one such property wrapper. The value will now be stored in a place where is *can* be changed.

A property wrapper wraps a property inside another struct. When we use `@State` to wrap a string, the actual type of property we end up with is a `State<String>`.



### Create State

```swift
@State var show = false
```

**Note:** Apple recommends we add `private` access control to those properties, like this: `@State private var tapCount = 0`.



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

Gestures can have multiple events like `onChanged` and `onEnded`. The events return values that can be translated to `CGSize`. (CG stands for Core Graphics)

## Animations

### Implicit Animations

What is an implicit animation? It's telling a `View` you want to animate it, and this is how you should respond.

```swift
// animations need to use CGFloat (similar to a Double) due to backwards compatability
@State private var animationAmount: CGFloat = 1

Button("Tap Me") {
  	// when tapped, increase scale by 1
    self.animationAmount += 1
}
.padding(50)
.background(Color.red)
.foregroundColor(.white)
.clipShape(Circle())
.scaleEffect(animationAmount)
// add the implicit animation -- it's a function of our state (we don't say when to start/stop it)
.animation(.default)
```



You can also trigger the animation with the `.onAppear` modifier:

```swift
...
.onAppear {
    self.animationAmount = 2
}
```



### Customizing Implicit Animations

- `.animation(.easeInOut(duration: 0.3))`

- `.animation(Animation.easeInOut.delay(0.3))`

- `.animation(.interpolatingSpring(stiffness: 50, damping: 1))`:  (`stiffness` is initial velocity and `damping` is how fast (lower values = bounces for longer))

Note, an animation at the child level takes precedence over an animation set at the parent level.



### Animations in Two-Way Bindings

Animations can be added to two-way bindings:

```swift
Stepper("Scale amount", value: $animationAmount.animation(
	Animation.easeInOut(duration: 1)
		.repeatCount(3, autoreverses: true)
), in: 1...10)
```

Here, the view has no idea it will be animated, but it works.



### Explicit Animations

You can also ask SwiftUI to animate changes occurring as the result of a state change.

```swift
struct ContentView: View {
  	// only a Double needed this time
    @State private var animationAmount = 0.0
    
    var body: some View {
        Button("Tap Me") {
          	// being explicit that this state change will trigger an animation
          	// use a withAnimation closure
            withAnimation {
                self.animationAmount += 360
            }
        }
        .padding(50)
        .background(Color.red)
        .foregroundColor(.white)
        .clipShape(Circle())
        .rotation3DEffect(.degrees(animationAmount), axis: (x: 0, y: 1, z: 0))
    }
}
```



`withAnimation` can be given an animation parameter:

```swift
withAnimation(.interpolatingSpring(stiffness: 5, damping: 1)) {
    self.animationAmount += 360
}
```



### Transition Options

In additional to normal timing options, there is a `.default` timing option that is similar to `.easeInOut`

Physics-based option: `animation(.spring(response: 0.3, dampingFraction: 0.6, blendDuration: 0))`

- The lower the  `.response`, the quicker the animation
- The `dampingFraction` regards the amount of bounce
- The `blendDuration` regards animation queuing???



There is also a `.transition` modifier:

- `.transition(.scale)`

- `.transition(.asymmetric(insertion: .scale, removal: .opacity))`



### Custom Transitions

[Link](https://www.hackingwithswift.com/books/ios-swiftui/building-custom-transitions-using-viewmodifier)

```swift
View
	.transition(.pivot)

struct CornerRotateModifier: ViewModifier {
    let amount: Double
    let anchor: UnitPoint

    func body(content: Content) -> some View {
        content.rotationEffect(.degrees(amount), anchor: anchor).clipped()
    }
}

extension AnyTransition {
    static var pivot: AnyTransition {
        .modifier(
            active: CornerRotateModifier(amount: -90, anchor: .topLeading),
            identity: CornerRotateModifier(amount: 0, anchor: .topLeading)
        )
    }
}
```



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



### Stopping Animations

If you don't want part of your stack animated, use `nil`:

```swift
Button("Tap Me") {
    self.enabled.toggle()
}
.frame(width: 200, height: 200)
.background(enabled ? Color.blue : Color.red)
.animation(nil)
.foregroundColor(.white)
.clipShape(RoundedRectangle(cornerRadius: enabled ? 60 : 0))
.animation(.interpolatingSpring(stiffness: 10, damping: 1))
```



### Animating Gestures

```swift
struct ContentView: View {
    @State private var dragAmount = CGSize.zero
    
    var body: some View {
        LinearGradient(gradient: Gradient(colors: [.yellow, .red]), startPoint: .topLeading, endPoint: .bottomTrailing)
            .frame(width: 300, height: 200)
            .clipShape(RoundedRectangle(cornerRadius: 10))
            .offset(dragAmount)
            .gesture(
                DragGesture()
                    .onChanged { self.dragAmount = $0.translation }
                    .onEnded { _ in self.dragAmount = .zero }
            )
    }
}
```



## Alerts

To show an alert:

```swift
struct ContentView: View {
    @State private var showingAlert = false
    
    var body: some View {
        Button("Show Alert") {
            self.showingAlert = true
        }
      	// With two-way binding, the dismiss button will set this back to false
        .alert(isPresented: $showingAlert) {
            Alert(title: Text("Hello SwiftUI"), message: Text("This is some detail message"), dismissButton: .default(Text("OK")))
        }
    }
}
```



## Conditionals

```swift
if (self.bottomState.height < -100 && !self.showFull) || (self.bottomState.height < -250 && self.showFull) {
    self.showFull = true
}
```



To load a certain view:

```swift
if showContent {
	ContentView()
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

### Linear Gradient

```swift
.background(LinearGradient(gradient: Gradient(colors: [Color.red, Color.blue]), startPoint: .top, endPoint: .bottom))
```

The `Color.red` can be replaced with `Color(Color Literal)` to allow you to input a hex code or pick from a color picker (double click on the literal)



Colors can also be written like this: `Color(red: 1, green: 1, blue: 0)`



### Radial Gradient

```swift
RadialGradient(gradient: Gradient(colors: [.blue, .black]), center: .center, startRadius: 20, endRadius: 200)
```



### Conical or Angular Gradient

```swift
AngularGradient(gradient: Gradient(colors: [.red, .yellow, .green, .blue, .purple, .red]), center: .center)
```



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

Two ways to create a button.

First, is with a title and a closure:

```swift
Button("Tap me!") {
    print("Button was tapped")
}
```

Second, is by passing in the contents. Useful if there is an image in the button:

```swift
Button(action: {
    print("Button was tapped")
}) { 
    Text("Tap me!")
}
```



A button:

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

`@Binding` creates a two-way data flow. `$` indicates passing in a binding, instead of a value.

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



If you need to bind to a file with a preview, you need to specify default settings. Use a **constant binding** which is a binding with a fixed value that can't be changed in the UI. Perfect for previews.

```swift
struct HomeView_Previews: PreviewProvider {
    static var previews: some View {
        HomeView(showProfile: .constant(false))
    }
}
```



## Two-Way Binding

An example of **two-way binding** is binding a text field to show the value of a property, and also binding it so that any changes to the text field updates the property. We use a `$` to create two-way bindings.

```swift
struct ContentView: View {
    @State private var name = ""

    var body: some View {
        Form {
            TextField("Enter your name", text: $name)
            Text("Hello World")
        }
    }
}
```



## Custom Bindings

You can specify different logic for the get and set property observors with custom bindings.



Simple Example:

```swift
struct ContentView: View {
    @State var selection = 0

    var body: some View {
        let binding = Binding(
          	// A simple passthrough
            get: { self.selection },
            set: { self.selection = $0 }
        )

        return VStack {
          	// No need to ask for a two-way binding since we are specifying that it is
            Picker("Select a number", selection: binding) {
                ForEach(0 ..< 3) {
                    Text("Item \($0)")
                }
            }.pickerStyle(SegmentedPickerStyle())
        }
    }
}
```



Advanced Example. Here switching `agreedToAll` will toggle the other toggles.

```swift
struct ContentView: View {
    @State var agreedToTerms = false
    @State var agreedToPrivacyPolicy = false
    @State var agreedToEmails = false

    var body: some View {
        let agreedToAll = Binding<Bool>(
            get: {
                self.agreedToTerms && self.agreedToPrivacyPolicy && self.agreedToEmails
            },
            set: {
                self.agreedToTerms = $0
                self.agreedToPrivacyPolicy = $0
                self.agreedToEmails = $0
            }
        )

        return VStack {
            Toggle(isOn: $agreedToTerms) {
                Text("Agree to terms")
            }

            Toggle(isOn: $agreedToPrivacyPolicy) {
                Text("Agree to privacy policy")
            }

            Toggle(isOn: $agreedToEmails) {
                Text("Agree to receive shipping emails")
            }

            Toggle(isOn: agreedToAll) {
                Text("Agree to all")
            }
        }
    }
}
```





## Detecting Screen Size

Declare `let screen = UIScreen.main.bounds` anywhere in the app. 

To use it: `.offset(y: showProfile ? 0 : screen.height)`



## Looping

`Cmd + Click` on your view and select `Repeat`. It will create 5 copies of the same element:

```swift
ForEach(0 ..< 5) { item in
	SectionView()
}
```

When using a `ForEach`, you are no longer limited to 10 children.

A `ForEach` is a `View` in itself, that returns other `Views`. 



To loop over items of an array:

```swift
ForEach(agents, id: \.self) {
	Text($0)
}
```

This is needed to tell Swift that items are all unique.



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

**Note**: Be careful. When you add views to a ScrollView, they are immedietly created. 



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



## 3D Transformations

The `GeometryReader` detects the size and position of your views. You'll need to add a frame with the size of your content.

```swift
ForEach(sectionData) { item in
	GeometryReader { geometry in
		SectionView(section: item)
	}
	.frame(width: 275, height: 275)
}
```

You now have access to the width, height, top left position, top right postion, etc.. of the view



To add a 3D rotation to the view:

```swift
SectionView(section: item)
	.rotation3DEffect(Angle(degrees:
		Double(geometry.frame(in:
			.global).minX - 30) / -20
	), axis: (x: 0, y: 10, z: 0))
```

This takes the frame value of the view and looks at the left postion (minX). These are `CGFloats` (needed instead of `Double` for backwards compatibility), so they'll need to be converted to a Double. The effect is damped by dividing it by 20. The effect only happens on the y-axis. Negating the 20 moves it to the opposite angle. 30 is subtracted to remove the original offset.



## Sheet Modals

Adds a sheet modal. When binding is true, the view is rendered within a sheet, that comes with gestures and animations. Use it for quick information.

You don't need a NavigationView and you don't need to add a dismiss interaction (user already understands the swipe dismiss gesture). Remove any gestures in the view to prevent gesture conflict.

```swift
.sheet(isPresented: $showUpdate) {
	ContentView()
}
```



## Lists

A List comes with a few style options: `DefaultListStyle()` or `GroupidListStyle()` (content is white, background is off-white)

```swift
List {
	VStack {
		Image(update.image)
		Text(update.text)
	}
	.navigationBarTitle(update.title)
}
.listStyle(GroupedListStyle())
```



You can mix static and dynamic content:

```swift
List {
    Text("Static row 1")
    Text("Static row 2")

    ForEach(0..<5) {
        Text("Dynamic row \($0)")
    }

    Text("Static row 3")
    Text("Static row 4")
}
```



If you have nothing but dynamic content, you can do this:

```swift
// range
List(0..<5) {
    Text("Dynamic row \($0)")
}

// array
struct ContentView: View {
    let people = ["Finn", "Leia", "Luke", "Rey"]

    var body: some View {
        List(people, id: \.self) {
            Text($0)
        }
    }
}
```



## Navigation View

A `NavigationView` comes with a back button, animation, and gestures to go back for each `NavigationLink`. There are also advanced interaction such as swipe to delete and edit mode.

```swift
NavigationView {
	NavigationLink(destination: Text("1")) {
		Text("SwiftUI")
	}
}
```

This can be embedded in a List and given a title (`.navigationBarTitle`). Note, this needs to be inside the `NavigationView`. The reason is that navigation views are capable of showing many views as your program runs, so by attaching the title to the thing *inside* the navigation view we’re allowing iOS to change titles freely.

They also have different types of `displayMode`s. 

<img src="https://juliette-images.s3.us-east-2.amazonaws.com/public/swift002.png" alt="Example" style="zoom:50%;" />



```swift
NavigationView {
	List(updateData) { update in
		NavigationLink(destination: Text(update.text)) {
			Text(update.title)
		}
	}
	.navigationBarTitle(Text("Updates"))
}
```

NavigationViews are very customizable:

<img src="https://juliette-images.s3.us-east-2.amazonaws.com/public/swift003.png" alt="Example" style="zoom:33%;" />



### Using Views in Navigator

1. Create a new view with a default value for the view

   ```swift
   struct UpdateDetail: View {
       var update: Update = updateData[0]
   
       var body: some View {
   			...
   ```

2. Pass in the value into the new view:

   ```swift
   List(updateData) { update in
   	NavigationLink(destination: UpdateDetail(update: update)) {
     	...
   ```

3. Use the value

   ```swift
   struct UpdateDetail: View {
       var update: Update = updateData[0]
   
       var body: some View {
           List {
               VStack {
                   Image(update.image)
                       .resizable()
                       .aspectRatio(contentMode: .fit)
                       .frame(maxWidth: .infinity)
                   Text(update.text)
                       .frame(maxWidth: .infinity, alignment: .leading)
               }
               .navigationBarTitle(update.title)
           }
           .listStyle(PlainListStyle())
       }
   }
   ```

   

### Navigation Buttons

Added to List:

```
.navigationBarItems(leading: Button(action: addUpdate) {
	Text("Button")
})
```



## Using Data Stores

### Create a Store

1. Create a new file with an ObservableObject. The `@Published` ensures that the data is constantly updated.

   ```swift
   import SwiftUI
   import Combine
   
   class UpdateStore: ObservableObject {
       @Published var updates: [Update] = updateData
   }
   
   ```

   `updates` is an array of the previously declared `Update` data type. It is set to a previously declared array of data called `updateData`

2. Use the store in a view.

   ```swift
   struct UpdateList: View {
       @ObservedObject var store = UpdateStore()
   
       var body: some View {
           NavigationView {
               List(store.updates) { update in
   							...
   }
   ```

   

### Adding to Store

1. Add a function:

   ```swift
   func addUpdate() {
   	store.updates.append(Update(image: "Card1", title: "New Item", text: "Text", date: "Jan 	1"))
   }
   ```

   

2. Use the function on a button:

   ```
   .navigationBarItems(leading: Button(action: addUpdate) {
   	Text("Add")
   })
   ```



### Deleting an Item

Needs to be used in a `ForEach`. There is a modifier that can be added to the `ForEach`:

```swift
.onDelete { index in
  // ! means the value is not optional
	self.store.updates.remove(at: index.first!)
}
```



### Editing an Item

An `EditButton()` can be added to the `.navigationBarItems` with built in edit functionality:

<img src="https://juliette-images.s3.us-east-2.amazonaws.com/public/swift004.png" alt="Example" style="zoom:50%;" />

Add a `.onMove` modifier to your navigation list items to enable reordering:

```swift
.onMove { (source: IndexSet, destination: Int) in
	self.store.updates.move(fromOffsets: source, toOffset: destination)
}
```



## Tab Bar

Bottom navigation. The best way to access other views. 

<img src="https://juliette-images.s3.us-east-2.amazonaws.com/public/swift005.png" alt="Example" style="zoom:50%;" />

```swift
struct TabBar: View {
    var body: some View {
        TabView {
            Home().tabItem {
                Image(systemName: "play.circle.fill")
                Text("Home")
            }
            ContentView().tabItem {
                Image(systemName: "rectangle.stack.fill")
                Text("Certificates")
            }
        }
    }
}
```



## Testing

### Setting Inital Screen

In `SceneDelegate.swift`, assign `contentView` to the first screen (e.g. `TabBar()`)



### Quick Preview

To quickly preview on a specific device:

```swift
struct TabBar_Previews: PreviewProvider {
    static var previews: some View {
        TabBar().previewDevice("iPhone 8")
    }
}

```



To preview on multiple devices (will not work with live preview):

```swift
struct TabBar_Previews: PreviewProvider {
    static var previews: some View {
        Group {
            TabBar().previewDevice("iPhone 8")
            TabBar().previewDevice("iPhone Xs")
        }
    }
}
```



Option + Click on `.previewDevice` to show all of your options.



### Testing on a Device

You may need to allow the device to be installed.

On your device: Go to `Settings > General > Profiles or Settings > General > Device Management`, trust the developer and allow the apps to be run.



## Variables within Components

If you have a variable that will not change, it needs to change to let and live within the view. The implicit return will need to replaced.

```swift
struct RingView: View {
    var width: CGFloat = 88

    var body: some View {
        let multiplier = width / 44
        
        return ZStack {
```



## Custom Modifiers

Create a `Modifiers.swift` file:

```swift
import SwiftUI

struct ShadowModifier: ViewModifier {
    func body(content: Content) -> some View {
        content
            .shadow(color: Color.black.opacity(0.2), radius: 20, x: 0, y: 20)
            .shadow(color: Color.black.opacity(0.1), radius: 1, x: 0, y: 1)
    }
}

```

Use the modifier:

```swift
.modifier(ShadowModifier())
```



If you add a modifier to a VStack or HStack, it will apply to all of the children.



You can also create an extension on a View to use this modifier:

```swift
extension View {
    func titleStyle() -> some View {
        self.modifier(Title())
    }
}

Text("Hello World")
    .titleStyle()
```





### Passing in Styles to a Modifier:

```swift
struct FontModifier: ViewModifier {
    var style: Font.TextStyle = .body

    func body(content: Content) -> some View {
        content
            .font(.system(style, design: .rounded))
    }
}

// Text("6 minutes left").bold().modifier(FontModifier(style: .subheadline))
```



## Custom Fonts

1. Download fonts (e.g. from Google Fonts)
2. Add to Project > App Name directory. Make sure `Copy items if needed`, `Create groups`, and `Add to  targets` are selected.
3. In `Info.plist`, add a`Fonts provided by application` property. For each item, name the file (e.g. `WorkSans-Regular.ttf`)



## Dates

### Simple Date

Swift has a built-in type for handling dates.

```swift
let now = Date()
let tomorrow = Date().addingTimeInterval(86400)
let range = now ... tomorrow
```



### DateComponents

Lets you write specific parts of a date:

```swift
var components = DateComponents()
components.hour = 8
components.minute = 0
let date = Calendar.current.date(from: components) ?? Date()
```



Lets you get parts from a date:

```swift
let components = Calendar.current.dateComponents([.hour, .minute], from: someDate)
let hour = components.hour ?? 0
let minute = components.minute ?? 0
```



### DateFormatter

To get the time from a date:

```swift
let formatter = DateFormatter()
formatter.timeStyle = .short
let dateString = formatter.string(from: Date())
```





## Core ML 

All iPhones come with a machine learning tool called `Core ML`.

1. `Xcode > Open Developer Tool > Create ML`
2. `New Document`

### Tabular Regressor

1. Under `Data Inputs`, select `Choose` under the `Training Data` title.
2. Choose a `csv` file to use
3. Chose the `target` - the value we want the computer to learn to predict
4. Select `features` to use for the training
5. Choose the `algorithm`. Start with `Automatic`.
   1. `Linear Regression` - The goal of a linear regression is to be able to draw one straight line through all your data points
   2. `Decision Tree` - Similar to a game of 20 questions
   3. `Boosted Tree` - A series of decision trees, each one correcting errors in the previous tree. First tree has 20% chance of guessing, next tree has 10%, etc..
   4. `Random Forest` - A boosted tree, but only has access to a subset of data. Combine all the predicitions together to make an average.
6. Press `Train`
7. In `Evaluation` you'll see the `Root Mean Square Error` - this is the average error.
8. In `Output` you'll see the size of your file.



### Using Predicitons

Always wrap in a `do {} try {}` since predictions may fail:

```swift
do {
	let prediction = try model.prediction(wake: Double(hour + minute), estimatedSleep: sleepAmount, coffee: Double(coffeeAmount))
	let sleepTime = wakeUp - prediction.actualSleep

	let formatter = DateFormatter()
	formatter.timeStyle = .short

	alertMessage = formatter.string(from: sleepTime)
	alertTitle = "Your ideal bedtime is..."
} catch {
	alertTitle = "Error"
	alertMessage = "Sorry, there was a problem calculating your bedtime."
}
```



## Adding Resources

For non-image resources, you need to import then from the bundle:

```swift
if let fileURL = Bundle.main.url(forResource: "some-file", withExtension: "txt") {
    // we found the file in our bundle!
}

// loading text file
if let fileContents = try? String(contentsOf: fileURL) {
    // we loaded the file into a string!
}
```



## Strings

### Turning a String into an Array

```swift
let input = "a b c"
let letters = input.components(separatedBy: " ")
```



##  Checking for Misspelled Words

```swift
// 1. Create a word and an instance of UITextChecker()
let word = "swift"
let checker = UITextChecker()

// 2. Tell Swift how much of the word you wish to check
// UITextChecker is written with Objective-C. Objective-C does not handle emojis.
// You need to create an Objective-C range instead.
let range = NSRange(location: 0, length: word.utf16.count)

// 3. Search for misspellings
let misspelledRange = checker.rangeOfMisspelledWord(in: word, range: range, startingAt: 0, wrap: false, language: "en")

// 4. Check for results
// If the range is empty, Objective-C sends back NSNotFound (no concept of optionals)
let allGood = misspelledRange.location == NSNotFound

```



## Additional Resources

- [Apple Tutorials](https://developer.apple.com/tutorials/swiftui/tutorials)
- [Hacking with Swift](https://www.hackingwithswift.com/quick-start/swiftui)
- [Miscellaneous Resources](https://github.com/Juanpe/About-SwiftUI)
- [iOS Dev Weekly Newsletter](https://iosdevweekly.com/)
- [iOS Goodies Newsletter](https://ios-goodies.com/)
- [design+code YouTube](https://www.youtube.com/channel/UCTIhfOopxukTIRkbXJ3kN-g)

